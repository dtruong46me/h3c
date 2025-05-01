
# Upgrading software

## About software upgrade

Software upgrade enables you to upgrade a
software version, add new features, and fix software bugs. This chapter
describes software types and release forms, compares software upgrade methods,
and provides the procedures for upgrading software from the CLI. 

### Software types

The following software types are available:

·     BootWare image—Also called the Boot ROM image. This image contains a basic segment
and an extended segment. 

¡     The
basic segment is the minimum code that bootstraps the system.

¡     The
extended segment enables hardware initialization and provides system management
menus. When the device cannot start up correctly, you can use the menus to load
software and the startup configuration file or manage files. 

Typically, the BootWare image is
integrated into the Boot image to avoid software compatibility errors.

·     Comware image—Includes the following image subcategories: 

¡     Boot image—A .bin file that contains
the Linux operating system kernel. It provides process management, memory
management, and file system management. 

¡     System image—A .bin file that
contains the Comware kernel and standard features, including device management,
interface management, configuration management, and routing.

¡     Feature image—A .bin file that
contains advanced or customized software features. You can purchase feature
images as needed.

¡     Patch image—A .bin file that is
released for fixing bugs without rebooting the device. A patch image does not
add or remove features. 

Patch images have the following types:

\-     Incremental patch images—A new patch image can cover all, part, or none of the functions
provided by an old patch image. A new patch image can coexist with an old patch
image on the device only when the former covers none of the functions provided
by the latter.

\-     Non-incremental patch images—A new non-incremental patch image covers all functions provided by
an old non-incremental patch image. Each boot, system, or feature image can
have one non-incremental patch image, and these patch images can coexist on the
device. The device uninstalls the old non-incremental patch image before
installing a new non-incremental patch image. 

An incremental patch image and a
non-incremental patch image can coexist on the device. 

Comware images that have been loaded are
called current software images. Comware images specified to load at the next
startup are called startup software images.

BootWare image, boot image, and system
image are required for the device to operate. 

You can install up to 32 .bin files on the
device, including one boot image file, one system image file, and up to 30
feature and patch image files.

### Software release forms

Software images are released in one of the
following forms:

·     Separate .bin files. You must verify
compatibility between software images.

·     As a whole in one .ipe package file. The images
in an .ipe package file are compatible. The system decompresses the file
automatically, loads the .bin images and sets them as startup software images. 

 

|  | NOTE:  Software image file names use the model-comware version-image type\-release format. This document uses boot.bin and system.bin as boot and system image file names. |
| --- | --- |

 

### Upgrade methods

| Upgrade method | Software types | Remarks |
| --- | --- | --- |
| Upgrading from the CLI by using the boot loader method | ·     BootWare image  ·     Comware images (excluding incremental patches) | This method is disruptive. You must reboot the entire device to complete the upgrade. || Performing an ISSU from the CLI | Comware images | This method enables a software upgrade with a minimum amount of downtime. Use this method if possible.  For more information about ISSU, see "Performing an ISSU." || Upgrading from the BootWare menu | ·     BootWare image  ·     Comware images | Use this method when the device cannot start up correctly.  To use this method, first connect to the console port and power cycle the device. Then, press Ctrl\+B at prompt to access the BootWare menu.  For more information about upgrading software from the BootWare menu, see the release notes for the software version.  IMPORTANT IMPORTANT:  Use this method only when you do not have any other choice. |



 

This chapter covers only upgrading software
from the CLI by using the boot loader method.

### Software image loading

### 

#### Startup software images

To upgrade software, you must specify the
upgrade files as the startup software images for the device to load at next
startup. You can specify two lists of software images: one main and one backup.
The device first loads the main startup software images. If the main startup
software images are not available, the devices loads the backup startup
software images.

#### Image loading process at startup

At startup, the device performs the
following operations after loading and initializing BootWare:

**1\.**Loads main images.

**2\.**If any main image does not exist or is
invalid, loads the backup images.

**3\.**If any backup image does not exist or is
invalid, the device cannot start up. 

## Digitally signed software images

The software images for the device are
digitally signed for authenticity and integrity verification. This mechanism
ensures that the software installed on the system is from a trusted source and
has not been tampered with in the transfer, storage, or installation phase. 

The system performs software digital
signature verification for authenticity and integrity in the following
situations:

·     Before the system loads a software image during
startup. If the digital signature verification fails, the system will not load
the image and you will receive a digital signature verification failure
message.

·     When you specify a software image to upgrade the
device from the BootWare menu. If the digital signature verification fails, the
system will not set the image for upgrade and you will receive a digital
signature verification failure message.

·     Before the system loads a BootWare image to the
Normal area of BootWare. If the digital signature verification fails, the
system will not load the image and you will receive a digital signature
verification failure message.

·     When you specify a software image as a startup
image through the boot loader. The system will verify the digital signature of
the image before it updates the startup image list with the specified image. If
the digital signature verification fails, the system will not update the
startup image list and you will receive a digital signature verification
failure message.

·     When you specify startup images for an ISSU. The
system verifies the digital signature of a software image for authenticity and
integrity before it sets and loads that image as a main startup image. If the
digital signature verification fails, the system will not set or load the image
as a main startup image and you will receive a digital signature verification
failure message. For more information about ISSU, see "Performing an
ISSU."

·     Before the system activates a feature or patch
image. If the digital signature verification fails, the system will not activate
the image and you will receive a digital signature verification failure
message.

## Restrictions and guidelines: Software upgrade

You cannot perform another upgrade
operation before an upgrade operation is completed. You can use the install, issu, and boot-loader commands to perform upgrade operations.

As a best practice, store the startup
images in a fixed storage medium. If you store the startup images in a hot
swappable storage medium, do not remove the hot swappable storage medium during
the startup process. 

## Upgrading device software by using the boot loader method

### Software upgrade tasks at a glance

To upgrade software, perform one of the
following tasks:

**1\.**Upgrade the IRF fabric:

**a.**(Optional.) [Preloading the BootWare image to
BootWare](#_Ref462920549)

If a BootWare upgrade is required, you
can perform this task to shorten the subsequent upgrade time. This task helps
reduce upgrade problems caused by unexpected power failure. If you skip this
task, the device upgrades the BootWare automatically when it upgrades the
startup software images.

**b.**[Specifying startup images and completing
the upgrade](#_Ref462920617)

**2\.**(Optional.) [Synchronizing startup images from the
master device to subordinate devices](#_Ref462908391)

Perform this task when the startup images
on subordinate devices are not the same version as those on the master device.

### Prerequisites

**1\.**Use the display version command to verify the current BootWare image version and startup
software version.

**2\.**Use the release notes for the upgrade
software version to evaluate the upgrade impact on your network and verify the
following items: 

¡     Software
and hardware compatibility.

¡     Version
and size of the upgrade software.

¡     Compatibility
of the upgrade software with the current BootWare image and startup software
image.

**3\.**Use the dir command
to verify that all IRF member devices have sufficient storage space for the
upgrade images. If the storage space is not sufficient on any member device,
delete unused files by using the delete
command. For more information, see "Managing file systems." 

**4\.**Use FTP or TFTP to transfer the upgrade
image file to the root directory of a file system. For more information about
FTP and TFTP, see "Configuring FTP" or "Configuring TFTP."
For more information about file systems, see "Managing file systems."

### Preloading the BootWare image to BootWare

Perform the following steps in user view: 

**5\.**Load the upgrade BootWare image to the
Normal area of BootWare.

bootrom update file file slot slot-number-list
\[ subslot subslot-number-list ]

Specify the downloaded software image
file for the file argument.

The subslot subslot-number-list option takes effect only if the switch is installed with an LSPM6FWD
subcard. 

The new BootWare image takes effect at a reboot.

 

|  | NOTE:  The system will verify the digital signature of a BootWare image before it loads it to the Normal area of BootWare. If the digital signature verification fails, the system will not load the image and you will receive a digital signature verification failure message. |
| --- | --- |

 

### Specifying startup images and completing the upgrade

Perform the following steps in user view:

**1\.**Specify main or backup startup images for
all member devices. 

¡     Use an .ipe file:

boot-loader file ipe-filename \[ patch filename\&\<1-16\> ] { all \| slot slot-number } { backup \| main }

¡     Use
.bin files:

boot-loader file boot filename system filename \[ feature filename\&\<1-30\> ] \[ patch filename\&\<1-16\> ] { all \| slot slot-number } { backup \| main }

As a best practice in a multichassis IRF
fabric, specify the all keyword for the
command. If you use the slot slot-number option to upgrade member devices one by one, version
inconsistencies occur among the member devices during the upgrade.

 

|  | NOTE:  The system will verify the digital signature of the specified images before it updates the startup image list with the specified images. If the digital signature verification fails, the system will not update the startup image list and you will receive a digital signature verification failure message. |
| --- | --- |

 

**2\.**Save the running configuration.

save

This step ensures that any configuration
you have made can survive a reboot.

**3\.**Reboot the IRF fabric.

reboot

**4\.**(Optional.) Verify the software image
settings.

display boot-loader \[ slot slot-number ]

Verify that the current software images are
the same as the startup software images.

### Synchronizing startup images from the master device to subordinate devices

#### About this task

Perform this task when the startup images
on subordinate devices are not the same version as those on the master device.

This task synchronizes startup images that
are running on the master device to subordinate devices. If any of the startup
images does not exist or is invalid, the synchronization fails.

The startup images synchronized to
subordinate devices are set as main startup images, regardless of whether the
source startup images are main or backup.

#### Restrictions and guidelines

If an ISSU or patch installation has been
performed on the master device, use the install commit command to update the set of main startup images on the master
device before software synchronization. This command ensures startup image
consistency between the master and subordinate devices.

#### Procedure

Perform the following steps in user view:

**1\.**Synchronize startup images from the master
to subordinate devices.

boot-loader update { all \| slot slot-number }

**2\.**Reboot the subordinate devices. 

reboot slot slot-number \[ force ]

## Display and maintenance commands for software image settings

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display current software images and startup software images. | display boot-loader \[ slot slot-number ] |

‌

## Software upgrade examples

### Example: Upgrading device software

#### Network configuration

As shown in [Figure 1](#_Ref479689980), use
the file startup-a2105.ipe to upgrade software
images for the IRF fabric.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704187_x_Img_x_png_1_2215959_294551_0.png)

 

#### Procedure

\# Configure IP addresses and routes. Make
sure the device and the TFTP server can reach each other. (Details not shown.)

\# Configure TFTP settings on both the
device and the TFTP server. (Details not shown.)

\# Display information about the current
software images.

\<Sysname\> display version

\# Back up the current software images.

\<Sysname\> copy boot.bin
boot\_backup.bin

\<Sysname\> copy system.bin
system\_backup.bin

\# Specify boot\_backup.bin
and system\_backup.bin as the backup startup image
files for all IRF member devices.

\<Sysname\> boot-loader file boot
flash:/boot\_backup.bin system flash:/system\_backup.bin slot 1 backup

\<Sysname\> boot-loader file boot
flash:/boot\_backup.bin system flash:/system\_backup.bin slot 2 backup

\# Use TFTP to download the startup-a2105.ipe image file from the TFTP server to the
root directory of the flash memory on the master device.

\<Sysname\> tftp 2.2.2.2 get
startup-a2105.ipe

\# Specify startup-a2105.ipe
as the main startup image file for all IRF member devices.

\<Sysname\> boot-loader file flash:/startup-a2105.ipe
slot 1 main

\<Sysname\> boot-loader file
flash:/startup-a2105.ipe slot 2 main

\# Verify the startup image settings.

\<Sysname\> display boot-loader

\# Reboot the device to complete the
upgrade.

\<Sysname\> reboot

\# Verify that the device is running the
correct software.

\<Sysname\> display version

 

