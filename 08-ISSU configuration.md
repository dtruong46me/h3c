
# Performing an ISSU

## About ISSU

The In-Service Software Upgrade (ISSU) feature upgrades the Comware software
with a minimum amount of downtime.

Unless otherwise stated, the term
"upgrade" refers to both software upgrade and downgrade in ISSU.

### ISSU advantages

The Comware software includes the boot
image, the system image, and feature images. ISSU supports upgrading the images
individually.

When you use ISSU to upgrade a feature,
only the related feature images are affected. Other features can continue to
provide services.

ISSU supports installing patch images to fix
system bugs without a system reboot.

### ISSU methods

ISSU
methods are determined at software release depending on the compatibility
between software versions.

ISSU supports the following upgrade types: 

·     Compatible upgrade—The new software version is compatible with the running software
version. This upgrade type supports the ISSU methods in [Table 1](#_Ref341883366).

·     Incompatible upgrade—The new software version is incompatible with the running software
version. The two versions cannot run concurrently.

This upgrade type supports only one
upgrade method (also called incompatible upgrade). This method requires a cold
reboot to upgrade both control and data planes. Incompatible upgrade disrupts service
if hardware redundancy is not available.

 

|  | NOTE:  For the compatibility matrix, see the ISSU upgrade type matrix in the release notes. |
| --- | --- |

 

For information about identifying the ISSU
method, see "[Identifying the ISSU methods](#_Ref390672695)."

Table 1 ISSU
methods for compatible upgrade

| ISSU method | Description |
| --- | --- |
| Incremental upgrade:  ·     Service Upgrade  ·     File Upgrade | Upgrades only user mode processes that have differences between the new and old software versions. Backup processes and a main/backup process switchover are required for service continuity.  ·     Service upgrade—Upgrades service features. The upgrade does not affect the operation of the features that are not being upgraded.  ·     File upgrade—Upgrades hidden system program files. The system can provide services during the upgrade. || Reboot | CAUTION CAUTION:  The Reboot method disrupts service if hardware redundancy is not available. As a best practice, schedule the downtime carefully to minimize the upgrade impact on the services.  Reboots member devices to complete the software upgrade. While one member device is rebooting, the other member devices can provide services. |


 

### ISSU commands

ISSU includes the install and issu command sets.
After you identify the recommended ISSU method, use [Table 2](#_Ref330384770) to
choose the command set you want to use.

Table 2 Commandset comparison

| Item | issu commands | install commands |
| --- | --- | --- |
| Upgrade types | ·     Compatible.  ·     Incompatible. | Compatible. || Impact on the system | Large. | Small. || Technical skill requirements | Low.  As a best practice, use this command set. | High.  Administrators must have extensive system knowledge and understand the impact of each upgrade task on the network. |



 

## Restrictions and guidelines: ISSU

| IMPORTANT | IMPORTANT:  ·     For a successful ISSU, you must remove all commands that the new version does not support and save the running configuration. To identify the feature changes between the current version and the new version, read the release notes for the device.  ·     To ensure correct system operation, you must remove the commands configured for features to be uninstalled and save the running configuration before uninstalling the features. |
| --- | --- |

 

During an ISSU, follow these restrictions
and guidelines:

·     You cannot perform another upgrade operation
before an upgrade operation is completed. You can use the install, issu, and boot-loader commands to perform upgrade operations.

·     Do not perform any of the following tasks:

¡     Reboot
member devices. 

¡     Perform
tasks that are irrelevant to the ISSU, such as modifying the configuration and
displaying information.

¡     Modify,
delete, or rename image files. 

·     You cannot use both install and issu commands for the
same ISSU.

·     For better service continuity, strictly follow
the recommended ISSU procedure unless you are performing an ISSU in one step.
Make sure a step is completed before you proceed to the next step.

·     Before executing the issu load command for the first time, use the display system
stable state command to verify that the system
is stable. If the System State field displays Stable, the system is stable.

·     Before executing the following commands, use the
display
system stable state command to verify that the
system is stable: 

¡     issu commands—issu load, issu run switchover,
and
issu commit.

¡     install commands—install
activate and install deactivate.

If the System State field displays Stable, the
system is stable.

·     You may use issu
commands to upgrade all or some of the software images. If you are upgrading
only some of the images, make sure the new images are compatible with the
images that are not to be upgraded. The upgrade will fail if a conflict exists.

·     You can install up to 32 .bin files on the
device, including one boot image file, one system image file, and up to 30
feature and patch image files.

·     A patch image file can be incremental or
non-incremental. You can install up to 30 incremental patch image files by
using a single command. Because each boot, system, or feature image can have
one non-incremental patch image file, you can install up to 16 non-incremental
patch image files by using a single command. For more information about
incremental and non-incremental patch image files, see "Upgrading software."

After an ISSU, you must log in to the
device again before you can use the changed or added commands.

After an ISSU, use the display
boot-loader command to verify that the new
software images have been successfully loaded. The startup software images must
always be in the flash memory and cannot be deleted. If you delete them, the
device cannot restart after being rebooted.

## Prerequisites for ISSU

To perform a successful ISSU, make sure all
the preparation requirements are met.

### Logging in to the device through the console port

Log in to the device through the console
port. If you use Telnet or SSH, you might be disconnected from the device
before the ISSU is completed.

In a multiuser environment, make sure no
other administrators access the device while you are performing the ISSU.

### Identifying availability of ISSU

Read the software release notes to identify
support of the device for ISSU between the current software version and the new
software version.

### Verifying the device operating status

Verify the following items:

Use the display device command to verify that all components are operating correctly.

·     Use the display irf
command to verify that all member devices are operating correctly.

·     Use the display cpu-usage command to verify that no CPU usage alarm exists on the device. As
a best practice, verify that the CPU usage is lower than the CPU usage recovery
threshold on each component.

·     Use the display memory and display memory-threshold commands to verify that no memory alarm exists on the device. As a
best practice, verify that the free memory is greater than the normal state threshold
on each component.

·     Use the display fan
command to verify that all fans are in Normal
state.

·     Use the display power command to verify that all power supplies are in Normal state.

·     Use the display environment command to verify that the temperature of each component is lower
than the high-temperature warning threshold.

·     Use the display counters rate inbound interface command to verify that all interfaces are forwarding packets
correctly.

·     Use the display interface \| include CRC command to verify that all interfaces are forwarding packets
correctly.

### Preparing the upgrade images

**1\.**Use the dir command
to verify that every file system has sufficient free storage space for the
upgrade images. If the storage space is not sufficient, delete unused files by
using the delete /unreserved file\-url command. If the files to be deleted will be used, back up the files
before deleting them. You will be unable to restore a deleted file if the /unreserved keyword is used. For more information, see "Managing file
systems."

**2\.**Use FTP or TFTP to transfer upgrade image
files (in .bin or .ipe) to the root directory of a file system on the master
device.

### Identifying the ISSU methods

**1\.**Execute the display version
comp-matrix file command to identify the recommended
ISSU methods.

¡     For a
compatible upgrade, check the Upgrade Way field to
identify the recommended ISSU methods.

¡     For an
incompatible upgrade, check the end of command output for the Incompatible upgrade string.

For more information about ISSU methods,
see [Table 1](#_Ref383618357).

### Verifying the device configuration

For a successful ISSU reboot or
incompatible upgrade and the IRF fabric integrity during the ISSU, a set of
features must have the same configuration as expected after the upgrade. The
following are the features and the commands for you to change and verify
feature settings:

·     Maximum number of ECMP routes.

max-ecmp-num

display max-ecmp-num

·     IPv4 enhanced ECMP mode.

ecmp mode enhanced

display ecmp mode

 

For more information about ECMP routes, see
basic IP routing configuration in Layer 3—IP Routing Configuration Guide.

### Verifying feature status

For service continuity during an ISSU,
configure the following feature settings:

 

| Feature | Setting requirements |
| --- | --- |
| GR and NSR | Enable GR or NSR for protocols including LDP, RSVP, OSPF, ISIS, BGP, and FSPF.  Use the display commands to verify that the system is in stable state after GR or NSR is enabled for a protocol. For example, use the display ospf graceful-restart or display ospf non-stop-routing status command to verify that the system is in stable state after GR or NSR is enabled for OSPF. || BFD | Disable BFD for protocols including LDP, RSVP, OSPF, IS-IS, RIP, BGP, VRRP, and NQA. || Ethernet link aggregation | Use the long LACP timeout interval (the lacp period short command is not configured) on all member ports in dynamic aggregation groups.  Make sure all member ports are Selected ports. || IRF | Before performing an incompatible upgrade for the IRF fabric, verify that IRF MAD is disabled. To use IRF MAD, enable IRF MAD after finishing the upgrade.  Configure IRF bridge MAC persistence as follows:  ·     Compatible upgrade—Execute the irf mac-address persistent timer or irf mac-address persistent always command.  ·     Incompatible upgrade—Execute the irf mac-address persistent always command if the bridge MAC address is the MAC address of the device for which you want to execute the issu load command. |




 

### Determining the upgrade procedure

**1\.**Use [Table 2](#_Ref383608250) to
choose an upgrade command set, depending on the ISSU method.

**2\.**Identify the hardware redundancy condition.

ISSU can maintain service continuity only
when the following conditions are met:

¡     The IRF
fabric has multiple members and uses the ring topology.

 

| IMPORTANT | IMPORTANT:  If hardware redundancy is not available, service discontinuity is not avoidable during a reboot upgrade or an incompatible upgrade. Make sure you understand the impact of the upgrade on the network. |
| --- | --- |

 

**3\.**Choose the correct procedure from the
procedures described in "[Performing an ISSU by using issu
commands](#_Ref384285056)" or "[Performing an ISSU by using install
commands](#_Ref384285080)." 

### Adjusting and saving the running configuration

**1\.**Remove all commands that the new software
version does not support from the running configuration. To identify all
feature changes between the current version and the new version, read the
release notes for the device.

**2\.**To uninstall a feature image, remove the
commands configured for the feature.

**3\.**Use the save command
to save the running configuration.

## Performing an ISSU by using issu commands

### Performing a compatible upgrade on a multichassis IRF fabric

#### Restrictions and guidelines

Upgrade a subordinate member device first.
Then, upgrade the remaining member devices, including the original master.

#### Procedure

**1\.**(Optional.) Configure automatic rollback:

**a.**Enter system view.

system-view

**b.**Set the automatic rollback timer.

issu rollback-timer minutes

By default, the automatic rollback timer
is set to 45 minutes.

The automatic rollback timer starts when
you execute the issu run switchover
command.

**c.**Return to user view.

quit

**2\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step.

**3\.**Load the upgrade images as startup images on
a subordinate member.

¡     Use
.bin files: 

issu load file { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number\&\<1-9\> \[ reboot ]

¡     Use
an .ipe file: 

issu load file ipe ipe-filename slot slot-number\&\<1-9\> \[ reboot ]

 

|  | NOTE:  The software images for the device are digitally signed. The system verifies the digital signatures of the specified software images for authenticity and integrity before it sets or loads them as main startup images. If the digital signature verification fails, the system will not set or load the specified images as main startup images and you will receive a digital signature verification failure message. |
| --- | --- |

 

**4\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step. If the system reported an error in the
previous step, verify that the system is stable and then use the issu rollback command to roll back the upgrade.

**5\.**Perform an ISSU switchover.

issu run switchover

This command also starts the automatic
rollback timer. When the timer expires, the system automatically rolls back to
the original software images.

**6\.**(Optional.) Accept the upgrade and delete
the automatic rollback timer.

issu accept

Execute this command before the automatic
rollback timer expires.

**7\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step. If the system reported an error in the
previous two steps, verify that the system is stable and then use the issu rollback command to roll back the upgrade.

**8\.**Upgrade the remaining members to complete
the ISSU.

issu commit slot slot-number

Execute this command before the automatic
rollback timer expires. 

 

| CAUTION | CAUTION:  Repeat the previous step and this step to upgrade the remaining member devices, including the original master device. After you execute this command for one member device, wait for the member device to start up and join the IRF fabric again before you upgrade another member device. This practice ensures a successful ISSU. |
| --- | --- |

 

**9\.**Verify that the ISSU is finished.

display issu state

If the ISSU state field displays Init, the ISSU
is finished.

### Performing an incompatible upgrade on a multichassis IRF fabric

#### Restrictions and guidelines

Upgrade one or more subordinate member
devices first. Then, upgrade the remaining member devices, including the
original master.

#### Procedure

To perform an incompatible upgrade on a
multichassis IRF fabric, execute the following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step.

**2\.**Load the upgrade images as startup images on
subordinate members.

¡     Use
.bin files: 

issu load file { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number\&\<1-9\> \[ reboot ]

¡     Use
an .ipe file: 

issu load file ipe ipe-filename slot slot-number\&\<1-9\> \[ reboot ]

As a best practice on a ring-topology IRF
fabric, specify half of the subordinate members for this command to reduce
service interruption. Make sure the specified subordinate members are
physically connected.

 

|  | NOTE:  The software images for the device are digitally signed. The system verifies the digital signatures of the specified software images for authenticity and integrity before it sets or loads them as main startup images. If the digital signature verification fails, the system will not set or load the specified images as main startup images and you will receive a digital signature verification failure message. |
| --- | --- |

 

**3\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step. If the system reported an error in the
previous step, verify that the system is stable and then use the issu rollback command to roll back the upgrade.

**4\.**Perform an ISSU switchover to complete the
ISSU process.

issu run switchover

This command upgrades the remaining
members.

**5\.**Verify that the ISSU is finished.

display issu state

If the ISSU state field displays Init, the ISSU
is finished.

### Performing an incremental upgrade on a single-chassis IRF fabric

To perform an incremental upgrade on a single-chassis
IRF fabric, execute the following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step.

**2\.**Load the upgrade images as startup images.

¡     Use
.bin files: 

issu load file { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number \[ reboot ]

¡     Use
an .ipe file: 

issu load file ipe ipe-filename slot slot-number \[ reboot ]

 

|  | NOTE:  The software images for the device are digitally signed. The system verifies the digital signatures of the specified software images for authenticity and integrity before it sets or loads them as main startup images. If the digital signature verification fails, the system will not set or load the specified images as main startup images and you will receive a digital signature verification failure message. |
| --- | --- |

 

**3\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step. If the system reported an error in the
previous step, verify that the system is stable and then use the issu rollback command to roll back the upgrade.

**4\.**Complete the ISSU process.

issu commit slot slot-number

 

| CAUTION | CAUTION:  When this command is completed, the ISSU status changes to Init and the ISSU process cannot be rolled back. |
| --- | --- |

 

**5\.**Verify that the ISSU is finished.

display issu state

If the ISSU state field displays Init, the ISSU
is finished.

### Performing a reboot or incompatible upgrade on a single-chassis IRF fabric

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step.

**2\.**Load the parent device's upgrade images as
startup images on subordinate members.

¡     Use
.bin files: 

issu load file { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number \[ reboot ]

¡     Use
an .ipe file: 

issu load file ipe ipe-filename slot slot-number \[ reboot ]

 

|  | NOTE:  The software images for the device are digitally signed. The system verifies the digital signatures of the specified software images for authenticity and integrity before it sets or loads them as main startup images. If the digital signature verification fails, the system will not set or load the specified images as main startup images and you will receive a digital signature verification failure message. |
| --- | --- |

 

**3\.**Verify that the ISSU is finished.

display issu state

If the ISSU state field displays Init, the ISSU
is finished.

## Performing an ISSU by using install commands

### ISSU tasks at a glance

**1\.**(Optional.) [Decompressing an .ipe file](#_Ref384215797)

**2\.**[Installing and upgrading software images](#_Ref320093913)

**3\.**(Optional.) [Deactivating software images](#_Ref384215825)

**4\.**(Optional.) [Rolling back the running software images](#_Ref384215888)

**5\.**(Optional.) [Aborting a software activate or
deactivate operation](#_Ref384215893)

**6\.**(Optional.) [Verifying software images](#_Ref384215912)

**7\.**[Committing software changes](#_Ref384215910)

**8\.**(Optional.) [Deleting inactive software images](#_Ref384215919)

### Decompressing an .ipe file

**1\.**(Optional.) Identify images that are
included in the .ipe file.

display install ipe-info

**2\.**Decompress the .ipe file.

install add ipe-filename filesystem

### Installing and upgrading software images

#### About this task

Use this task to install new feature and
patch images or upgrade the boot, system, or feature images.

#### Software image installation and upgrade methods

Use one of the following methods to install
or upgrade software images:

·     Slot by slot—Activate all the images on one slot, and then move to the next
slot.

·     Image by image—Activate one image on all slots before moving to another image.
Before using this method, read the release notes to make sure the upgrade
images are compatible with the current images. If the upgrade images are not
compatible with the current images, the upgrade might fail.

#### Restrictions and guidelines

To install an image, you must begin with
the master device. To upgrade an image, you must begin with a subordinate device.

#### Installing or upgrading boot, system, and feature images

Execute the following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful ISSU, you must make sure the system is stable
before you proceed to the next step.

**2\.**(Optional.) Identify the recommended ISSU
method and the possible impact of the upgrade.

install activate { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number test

**3\.**Activate images.

install activate { boot filename \| system filename \| feature filename\&\<1-30\> } \* slot slot-number

 

|  | NOTE:  The software images for the device are digitally signed. The system will verify the digital signatures of the specified software images for authenticity and integrity before it activates them. If the digital signature verification fails, the system will not activate the specified images and you will receive a digital signature verification failure message. |
| --- | --- |

 

#### Installing patch images

To install patch images, execute the
following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful installation, you must make sure the system is
stable before you proceed to the next step.

**2\.**Install patch images.

install activate patch filename { all \| slot slot-number }

You can specify only one patch image file
for this command at a time. However, you can execute this command multiple
times to install multiple patch image files.

The install activate patch filename all command installs the specified patch images on all hardware and the
images can survive a reboot. You do not need to execute the install commit command for the
installation.

 

|  | NOTE:  The software images for the device are digitally signed. The system verifies the digital signature of a patch image for authenticity and integrity before it activates it. If the digital signature verification fails, the system will not activate the image and you will receive a digital signature verification failure message. |
| --- | --- |

 

### Deactivating software images

#### Restrictions and guidelines

You can deactivate only feature and patch
images.

The deactivate operation only removes
images from the current software image list. For the image changes to take
effect after a reboot, you must perform a commit operation to remove the images
from the main startup image list.

Deactivated images are still stored on the
storage medium. To permanently delete the images, execute the install remove command. For more information, see "[Deleting inactive software images](#_Ref384212844)."

#### Deactivating feature images

To deactivate feature images, execute the
following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful deactivate operation, you must make sure the
system is stable before you proceed to the next step.

**2\.**Deactivate feature images.

install deactivate feature filename\&\<1-30\> slot slot-number

#### Deactivating patch images

To deactivate patch images, execute the
following commands in user view:

**1\.**Verify that the system is stable.

display system stable state

The system is stable if the System State
field displays Stable. For a successful deactivate operation, you must make sure the
system is stable before you proceed to the next step.

**2\.**Deactivate patch images.

install deactivate patch filename { all \| slot slot-number }

You can specify only one patch image file
for this command at a time. However, you can execute this command multiple
times to deactivate multiple patch image files.

The install deactivate
patch filename all command
deactivates the specified patch images on all hardware and the image changes
can survive a reboot. You do not need to execute the install commit command for the
deactivation.

### Rolling back the running software images

#### About this task

During an incremental upgrade, the system
creates a rollback point for each activate or deactivate operation of a boot,
system, or feature image. The system can maintain a
maximum of 50 rollback points. If this limit has been reached when a rollback
point is created, the system removes the earliest rollback point. You can roll
back the software to any of the rollback points.

During a reboot or ISSU reboot upgrade, the
system does not create rollback points. After an ISSU reboot or reboot upgrade,
you can roll back the software only to the status before any activate or
deactivate operations were performed. 

#### Restrictions and guidelines

You can perform this task only before committing
software changes. A commit operation deletes all rollback points.

For an incremental upgrade rollback to take
effect after a reboot, you must perform a commit operation to update the main
startup image list.

#### Procedure

To roll back the running software images,
execute the following commands in user view:

**1\.**(Optional.) Display available rollback
points.

display install rollback

**2\.**Roll back the software.

install rollback to { point-id \| original }

### Aborting a software activate or deactivate operation

#### About this task

While the system is activating or deactivating
a software image for a service upgrade or file upgrade, you can abort the activate
or deactivate operation. After an operation is aborted, the system runs with
the software images that it was running with before the operation.

#### Procedure

To abort a software activate or deactivate
operation, use one of the following methods:

·     Execute the install abort \[ job-id ] command in user view.

·     Press Ctrl\+C.

### Committing software changes

#### About this task

When you activate or deactivate images for
an incremental upgrade, or activate or deactivate patch images, the main
startup image list does not update with the changes. The software changes are
lost at reboot. For the changes to take effect after a reboot, you must commit
the changes.

#### Procedure

To commit software changes, execute the
following command in user view:

install commit

### Verifying software images

#### About this task

Perform this task to verify the following
items:

·     Integrity—Verify that the boot, system, and feature images are integral.

·     Consistency—Verify that the same active images are running across the entire
system.

·     Software commit status—Verify that the active images are committed as needed.

#### Procedure

To verify software images, execute the
following commands in user view:

**1\.**Verify software images.

install verify

**2\.**Activate or deactivate images as required.

install { activate \| deactivate }

**3\.**Commit the software changes.

install commit

### Deleting inactive software images

#### About this task

After completing an ISSU, you can use this
task to delete old image files permanently.

#### Restrictions and guidelines

This task deletes image files permanently. You
cannot use the install rollback to
command to revert the operation, or use the install abort command to abort the operation.

#### Procedure

To delete inactive software image files,
execute the following command in user view:

install remove \[ slot slot-number ] { filename \| inactive }

## Terminating the ongoing ISSU process forcibly

#### About this task

Use this feature to terminate the ISSU
process if one of the following exceptions occurs and you cannot perform an
upgrade or rollback:

·     The ISSU status is not Init
but the upgrade has stopped.

·     The ISSU status is Init
but the upgrade has not completed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Terminate the ongoing ISSU process forcibly.

issu quit

## Display and maintenance commands for ISSU

| CAUTION | CAUTION:  The reset install rollback oldest command clears the specified rollback point and all rollback points earlier than the specified rollback point. You will be unable to roll back the configuration to the status when any of these rollback points was created. |
| --- | --- |

 

Unless otherwise stated, the display and reset commands can be
used during an ISSU, regardless of whether the install or issu commands are used.

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command | Remarks |
| --- | --- | --- |
| Display active software images. | display install active \[ slot slot-number ] \[ verbose ] | N/A || Display backup startup software images. | display install backup \[ slot slot-number ] \[ verbose ] | N/A || Display main startup software images. | display install committed \[ slot slot-number ] \[ verbose ] | N/A || Display inactive software images in the root directories of file systems. | display install inactive \[ slot slot-number ] \[ verbose ] | N/A || Display the software images included in an .ipe file. | display install ipe-info ipe-filename | N/A || Display ongoing ISSU activate, deactivate, and rollback operations. | display install job | N/A || Display ISSU log entries. | display install log \[ log-id ] \[ verbose ] | N/A || Display software image file information. | display install package { filename \| all } \[ verbose ] | N/A || Display rollback point information. | display install rollback \[ point-id ] | The system does not record rollback points during an ISSU that uses issu commands. || Display the software image file that includes a specific component or file. | display install which { component name \| file filename } \[ slot slot-number ] | N/A || Display automatic rollback timer information. | display issu rollback-timer | N/A || Display ISSU status information. | display issu state | This command applies only to an ISSU that uses issu commands. || Display the ISSU methods. | display version comp-matrix file { boot filename \| system filename \| feature filename\&\<1-30\> \| patch filename\&\<1-30\> } \*  display version comp-matrix file ipe ipe-filename \[ patch filename\&\<1-30\> ] | N/A || Clear ISSU log entries. | reset install log-history oldest log-number | N/A || Clear ISSU rollback points. | reset install rollback oldest point-id | N/A |















‌

## Examples of using issu commands for ISSU

### Example: Upgrading the system software to a compatible version

#### Network configuration

As shown in [Figure 1](#_Ref376015164),
the IRF fabric has two members.

Upgrade the boot, system, and feature images
from T0001015 to T0001016. The two versions are compatible.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704195_x_Img_x_png_6_2215960_294551_0.png)

 

#### Procedure

\# Download the upgrade image files from the
TFTP server.

\<Sysname\> tftp 2.2.2.2 get boot-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   6078464  100  6078464     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
system-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   97750016  100  97750016     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
feature-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   1008640  100  1008640     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

\# Identify the recommended ISSU methods.

\<Sysname\> display version
comp-matrix file boot flash:/boot-t0001016.bin system
flash:/system-t0001016.bin feature flash:/feature-t0001016.bin

Verifying the file
flash:/boot-t0001016.bin on slot 1.......................Done.

Verifying the file flash:/system-t0001016.bin
on slot 1................. .................Done.

Verifying the file
flash:/feature-t0001016.bin on slot 1.................Done.

Identifying the upgrade
methods....Done.

 

  Slot                        Upgrade
Way

  1                           Reboot

  2                           Reboot

The output shows that reboot upgrades are recommended.

\# Save the running configuration.

\<Sysname\> save

\# Upgrade the system software on the
subordinate member.

\<Sysname\> issu load file boot flash:/boot-t0001016.bin
system flash:/system-t0001016.bin feature flash:/feature-t0001016.bin slot 2

This operation will delete the
rollback point information for the previous upgrade and maybe get unsaved
configuration lost. Continue? \[Y/N]:y

Copying file flash:/boot-t0001016.bin
to slot2#flash:/boot-t0001016.bin......Done.

Copying file flash:/system-t0001016.bin
to slot2#flash:/system-t0001016.bin........................Done.

Copying file flash:/feature-t0001016.bin
to slot2#flash:/feature-t0001016.bin......Done.

Verifying the file flash:/boot-t0001016.bin
on slot 2...Done.

Verifying the file flash:/system-t0001016.bin
on slot 2............Done.

Verifying the file flash:/feature-t0001016.bin
on slot 2...Done.

Identifying the upgrade methods….Done.

Upgrade summary according to
following table:

 

flash:/boot-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/system-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/feature-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  2                          
Reboot

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

\# Verify that the ISSU is in a stable state.

\<Sysname\> display issu state 

ISSU state: Loaded

Compatibility: Compatible

Work state: Normal

Upgrade method: Card by card

Upgraded slot:

  slot 2

Current upgrading slot: None

Previous version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Previous software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Upgrade version list:

  boot: 7.1.070, Test 0001016

  system: 7.1.070, Test 0001016

  feature: 7.1.070, Test 0001016

Upgrade software images:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature-t0001016.bin

The Loaded
state is a stable state, which indicates that the system is waiting for a
master/subordinate switchover. Slot 2 has completed the upgrade, and slot 1 has
not.

\# Perform a master/subordinate switchover.

\<Sysname\> issu run switchover

Upgrade summary according to
following table:

 

flash:/boot-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/system-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/feature-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                       
Switchover Way

  1                           Master
subordinate switchover

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait.........

\# Verify that the ISSU is in a stable state.

\<Sysname\> display issu state

ISSU state: Switchover

Compatibility: Compatible

Work state: Normal

Upgrade method: Card by card

Upgraded slot:

  slot 2

Current upgrading slot: None

Previous version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Previous software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Upgrade version list:

  boot: 7.1.070, Test 0001016

  system: 7.1.070, Test 0001016

  feature: 7.1.070, Test 0001016

Upgrade software images:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature-t0001016.bin

The Switchover
state is a stable state, which indicates that the system has completed the master/subordinate
switchover and is waiting for a commit operation to upgrade slot 1\.

\# Upgrade the system software on the
original master.

\<Sysname\> issu commit slot 1

Upgrade summary according to
following table:

 

flash:/boot-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/system-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/feature-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  1                           Reboot

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

#### Verifying the upgrade

\# Verify that the ISSU is finished.

\<Sysname\> display issu state

ISSU state: Init

Compatibility: Unknown

Work state: Normal

Upgrade method: Card by card

Upgraded slot: None

Current upgrading slot: None

Current version list:

  boot: 7.1.070, Test 0001016

  system: 7.1.070, Test 0001016

  feature: 7.1.070, Test 0001016

Current software images:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature-t0001016.bin

The ISSU state
field displays Init, which indicates that the ISSU
is finished.

\# Verify that both members are running the
new software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature\-t0001016.bin

Active packages on slot 2:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature\-t0001016.bin

### Example: Upgrading the system software to an incompatible version

#### Network configuration

As shown in [Figure 2](#_Ref376015258),
the IRF fabric has two members.

Upgrade the boot, system, and feature
images from T0001015 to T0001017, which is an incompatible version.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704196_x_Img_x_png_7_2215960_294551_0.png)

 

#### Procedure

\# Download the upgrade image files from the
TFTP server.

\<Sysname\> tftp 2.2.2.2 get boot-t0001017.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   6078464  100  6078464     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
system-t0001017.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   97750016  100  97750016     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
feature-t0001017.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   1008640  100  1008640     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

\# Identify the recommended ISSU methods.

\<Sysname\> display version
comp-matrix file boot flash:/boot-t0001017.bin system flash:/system-t0001017.bin
feature flash:/feature-t0001017.bin

Verifying the file
flash:/boot-t0001017.bin on slot 1.......................Done.

Verifying the file flash:/system-t0001017.bin
on slot 1................. .................Done.

Verifying the file flash:/feature-t0001017.bin
on slot 1.................Done.

Identifying the upgrade
methods....Done.

 

Incompatible upgrade.

The output shows that an incompatible
upgrade is recommended.

\# Save the running configuration.

\<Sysname\> save

\# Upgrade the system software on the
subordinate member.

\<Sysname\> issu load file boot flash:/boot-t0001017.bin
system flash:/system-t0001017.bin feature flash:/feature-t0001017.bin slot 2

This operation will delete the
rollback point information for the previous upgrade and maybe get unsaved
configuration lost. Continue? \[Y/N]:y

Copying file flash:/boot-t0001017.bin
to slot2#flash:/boot-t0001017.bin......Done.

Copying file flash:/system-t0001017.bin
to slot2#flash:/system-t0001017.bin........................Done.

Copying file flash:/feature-t0001017.bin
to slot2#flash:/feature-t0001017.bin......Done.

Verifying the file flash:/boot-t0001017.bin
on slot 2...Done.

Verifying the file flash:/system-t0001017.bin
on slot 2............Done.

Verifying the file flash:/feature-t0001017.bin
on slot 2...Done.

Identifying the upgrade methods….Done.

Upgrade summary according to
following table:

 

flash:/boot-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

flash:/system-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

flash:/feature-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

  Slot                        Upgrade
Way

  2                           Reboot

Upgrading software images to
incompatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

\# Verify that the ISSU is in a stable state.

\<Sysname\> display issu state 

ISSU state: Loaded

Compatibility: Incompatible

Work state: Normal

Upgrade method: Card by card

Upgraded slot:

  slot 2

Current upgrading slot: None

Previous version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Previous software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Upgrade version list:

  boot: 7.1.070, Test 0001017

  system: 7.1.070, Test 0001017

  feature: 7.1.070, Test 0001017

Upgrade software images:

  flash:/boot-t0001017.bin

  flash:/system-t0001017.bin

  flash:/feature-t0001017.bin

The Loaded
state is a stable state, which indicates that the system is waiting for a
master/subordinate switchover. Slot 2 has completed the upgrade, and slot 1 has
not.

\# Perform a master/subordinate switchover.

\<Sysname\> issu run switchover

Upgrade summary according to
following table: 

 

flash:/boot-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

flash:/system-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

flash:/feature-t0001017.bin

  Running Version             New
Version

  Test 0001015                Test
0001017

 

  Slot                       
Switchover Way

  1                           Master
subordinate switchover

Upgrading software images to
incompatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

#### Verifying the upgrade

\# Verify that the ISSU is finished.

\<Sysname\> display issu state

ISSU state: Init

Compatibility: Unknown

Work state: Normal

Upgrade method: Card by card

Upgraded slot: None

Current upgrading slot: None

Current version list:

  boot: 7.1.070, Test 0001017

  system: 7.1.070, Test 0001017

  feature: 7.1.070, Test 0001017

Current software images:

  flash:/boot-t0001017.bin

  flash:/system-t0001017.bin

  flash:/feature-t0001017.bin

The ISSU state
field displays Init, which indicates that the ISSU
is finished.

\# Verify that both members are running the
new software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001017.bin

  flash:/system-t0001017.bin

  flash:/feature-t0001017.bin

Active packages on slot 2:

  flash:/boot-t0001017.bin

  flash:/system-t0001017.bin

  flash:/feature-t0001017.bin

### Example: Rolling back the system software

#### Network configuration

As shown in [Figure 3](#_Ref376015279),
the IRF fabric has two members.

Roll back the boot, system, and feature
images from T0001016 to T0001015 after upgrading them from T0001015 to T0001016.
T0001016 and T0001015 are compatible.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704197_x_Img_x_png_8_2215960_294551_0.png)

 

#### Procedure

\# Download the upgrade image files from the
TFTP server.

\<Sysname\> tftp 2.2.2.2 get boot-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   6078464  100  6078464     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
system-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   97750016  100  97750016     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\<Sysname\> tftp 2.2.2.2 get
feature-t0001016.bin

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   1008640  100  1008640     0    
0    764      0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

\# Identify the recommended ISSU methods.

\<Sysname\> display version
comp-matrix file boot flash:/boot-t0001016.bin system
flash:/system-t0001016.bin feature flash:/feature-t0001016.bin

Verifying the file flash:/all.ipe on
slot 1................. .................Done.

Identifying the upgrade
methods....Done.

 

  Slot                        Upgrade
Way

  1                           Reboot

  2                           Reboot

The output shows that reboot upgrades are recommended.

\# Save the running configuration.

\<Sysname\> save

\# Upgrade the system software on the
subordinate member.

\<Sysname\> issu load file boot flash:/boot-t0001016.bin
system flash:/system-t0001016.bin feature flash:/feature-t0001016.bin slot 2

This operation will delete the
rollback point information for the previous upgrade and maybe get unsaved
configuration lost. Continue? \[Y/N]:y

Copying file flash:/boot-t0001016.bin
to slot2#flash:/boot-t0001016.bin......Done.

Copying file flash:/system-t0001016.bin
to slot2#flash:/system-t0001016.bin........................Done.

Copying file flash:/feature-t0001016.bin
to slot2#flash:/feature-t0001016.bin......Done.

Verifying the file flash:/boot-t0001016.bin
on slot 2...Done.

Verifying the file flash:/system-t0001016.bin
on slot 2............Done.

Verifying the file flash:/feature-t0001016.bin
on slot 2...Done.

Identifying the upgrade methods….Done.

Upgrade summary according to
following table:

 

flash:/boot-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/system-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/feature-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  2                           Reboot

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

\# Verify that the ISSU is in a stable state.

\<Sysname\> display issu state 

ISSU state: Loaded

Compatibility: Compatible

Work state: Normal

Upgrade method: Card by card

Upgraded slot:

  slot 2

Current upgrading slot: None

Previous version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Previous software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Upgrade version list:

  boot: 7.1.070, Test 0001016

  system: 7.1.070, Test 0001016

  feature: 7.1.070, Test 0001016

Upgrade software images:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature-t0001016.bin

The Loaded
state is a stable state, which indicates that the system is waiting for a
master/subordinate switchover. Slot 2 has completed the upgrade, and slot 1 has
not.

\# Perform a master/subordinate switchover.

\<Sysname\> issu run switchover

Upgrade summary according to
following table:

 

flash:/boot-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/system-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

flash:/feature-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                       
Switchover Way

  1                           Master
subordinate switchover

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait.........

\# Verify that the ISSU is in a stable state.

\<Sysname\> display issu state 

ISSU state: Switchover

Compatibility: Compatible

Work state: Normal

Upgrade method: Card by card

Upgraded slot:

  slot
2

Current upgrading slot: None

Previous version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Previous software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

Upgrade version list:

  boot: 7.1.070, Test 0001016

  system: 7.1.070, Test 0001016

  feature: 7.1.070, Test 0001016

Upgrade software images:

  flash:/boot-t0001016.bin

  flash:/system-t0001016.bin

  flash:/feature-t0001016.bin

The Switchover
state is a stable state, which indicates that the system has completed the
master/subordinate switchover and is waiting for a commit operation to upgrade
slot 1\.

\# Roll back the software images to
T0001015.

\<Sysname\> issu rollback

This command will quit the ISSU
process and roll back to the previous version. Continue? \[Y/N]:Y

\# Verify that the rollback is finished.

\<Sysname\> display issu state

ISSU state: Init

Compatibility: Unknown

Work state: Normal

Upgrade method: Card by card

Upgraded slot: None

Current upgrading slot: None

Current version list:

  boot: 7.1.070, Test 0001015

  system: 7.1.070, Test 0001015

  feature: 7.1.070, Test 0001015

Current software images:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature-t0001015.bin

The ISSU state
field displays Init, which indicates that the rollback
is finished.

\# Verify that both members are running the
old software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature\-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature\-t0001015.bin

## Examples of using install commands for ISSU

### Example: Upgrading the system software

#### Network configuration

As shown in [Figure 4](#_Ref17896283), the IRF
fabric has two members. 

Upgrade the Feature1 feature image from
T0001015 to T0001016. The two versions are compatible.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704198_x_Img_x_png_9_2215960_294551_0.png)

 

#### Procedure

\# Download the .ipe file that contains the
T0001016 Feature1 feature image from the TFTP server.

\<Sysname\> tftp 2.2.2.2 get
feature1-t0001016.ipe

  % Total    % Received % Xferd 
Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100   256  100  256     0     0    764     
0 --:--:-- --:--:-- --:--:--   810

Writing file...Done.

\# Decompress the .ipe file.

\<Sysname\> install add flash:/feature1-t0001016.ipe
flash:

Verifying the file flash:/feature1-t0001016.ipe
on slot 1...Done.

Decompressing file feature1-t0001016.bin
to flash:/feature1-t0001016.bin.......................Done.

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

\# Identify the recommended ISSU methods and
possible impact of the upgrade.

\<Sysname\> install activate feature
flash:/feature1-t0001016.bin slot 2 test

Copying file flash:/feature1-t0001016.bin
to slot2#flash:/feature1-t0001016.bin......Done.

Verifying the file flash:/feature1-t0001016.bin
on slot 2...Done.

Upgrade summary according to
following table:

 

flash:/feature1-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  2                          
Service Upgrade

 

Influenced service according to
following table on slot 2:

  flash:/feature1-t0001016.bin

         Feature1

\<Sysname\> install activate
feature flash:/feature1-t0001016.bin slot 1 test

Verifying the file flash:/feature1-t0001016.bin
on slot 1...Done.

Upgrade summary according to
following table:

 

flash:/feature1-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  1                          
Service Upgrade

 

Influenced service according to
following table on slot 1:

  flash:/feature1-t0001016.bin

         Feature1

The output shows that both members need a
service upgrade and the Feature1 module will be rebooted during the upgrade.

\# Activate the new Feature1 image to
upgrade the Feature1 feature.

\<Sysname\> install activate
feature flash:/feature1-t0001016.bin slot 2

Verifying the file flash:/feature1-t0001016.bin
on slot 1...Done.

flash:/feature1-t0001016.bin already
exists on slot 2\.

Overwrite it?\[Y/N]:y

Copying file flash:/feature1-t0001016.bin
to slot2#flash:/feature1-t0001016.bin......Done.

Verifying the file flash:/feature1-t0001016.bin
on slot 2...Done.

Upgrade summary according to
following table:

 

flash:/feature1-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  2                           Service
Upgrade

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

\<Sysname\> install activate
feature flash:/feature1-t0001016.bin slot 1

Verifying the file flash:/feature1-t0001016.bin
on slot 1...Done.

Upgrade summary according to
following table:

 

flash:/feature1-t0001016.bin

  Running Version             New
Version

  Test 0001015                Test
0001016

 

  Slot                        Upgrade
Way

  1                           Service
Upgrade

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take several
minutes, please wait...Done.

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

\# Commit the software changes.

\<Sysname\> install commit

This operation will take several
minutes, please wait...........................Done.

The current software images have been
saved as the startup software images.

Current software images on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

Current software images on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

### Example: Rolling back a feature

#### Network configuration

As shown in [Figure 4](#_Ref17896283), the IRF
fabric has two members. The Feature1 feature has been upgraded from T0001015 to
T0001016. However, the software change has not been committed.

Roll back the Feature1 feature from
T0001016 to T0001015.

#### Procedure

\# Display active software images.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

Active packages on slot2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001016.bin

\# Display available rollback points.

\<Sysname\> display install
rollback

  Install rollback information 1 on
slot 1:

    Updating from flash:/feature1-t0001015.bin

             to flash:/feature1-t0001016.bin.

  Install rollback information 2 on
slot 2:

    Updating from flash:/feature1-t0001015.bin

             to flash:/feature1-t0001016.bin.

\# Roll back the Feature1 feature to
T0001015.

\<Sysname\> install rollback to original


This operation might take several
minutes, please wait...Done.

\# Verify that the IRF members are running
the old Feature1 image.

\<Sysname\> display install
active

Active packages on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

Active packages on slot 2:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

\# Commit the software changes.

\<Sysname\> install commit

This operation will take several
minutes, please wait...........................Done.

The current software images have been
saved as the startup software images.

Current software images on slot 0:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

Current software images on slot 1:

  flash:/boot-t0001015.bin

  flash:/system-t0001015.bin

  flash:/feature1-t0001015.bin

 

 

