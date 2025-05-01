
# Managing licenses

## About licenses

To use license-based features, you need to
purchase licenses from H3C and install the licenses.

To obtain information about license-based
features, their licensing status, and license availability, execute the display license
feature command on the device. Then, you can
purchase and install licenses as needed.

## License types

H3C offers preinstalled,
trial (or temporary), and formal licenses. Support for preinstalled and trial
licenses varies by device model. For more information about the license options
available for the device, see the license matrixes document for the device.

#### Preinstalled licenses

Preinstalled licenses are built into a
system and are available for use on initial startup of the system. Users do not
need to activate them.

A preinstalled license is typically time
limited and cannot be uninstalled or transferred. 

When a preinstalled license expires, the
license-based feature becomes unavailable. To continue to use the feature, you
must purchase and install a formal license for it.

#### Trial licenses

Trial licenses are provided for you to
verify the functionality of premium features so you can make an educated
purchase decision. 

A trial license is time limited and cannot
be uninstalled or transferred.

When a trial license expires, the
license-based feature becomes unavailable. To continue to use the feature, you
must purchase and install a formal license for it.

To obtain a trial license for a feature:

**1\.**Contact your H3C sales representative or
technical support.

**2\.**Register the trial license key with H3C License
Management Platform to obtain an activation file.

**3\.**Install the activation file to activate the
feature.

#### Formal licenses

To gain most from a license-based feature,
purchase a formal license for it. 

Formal licenses are available with
different validity periods and their support for uninstallation and transfer of
formal licenses depends on the device model. For more information, see the
license matrixes document for the device.

To install a formal license for a feature:

**1\.**Purchase a software license certificate
through an official channel.

**2\.**Access H3C License Management Platform, and
then enter the license key in the certificate and the required device
information to obtain an activation file.

**3\.**Install the activation file in the target
system.

## Basic concepts

The following information describes the
basic concepts that you might encounter when you register, install, and manage
licenses.

#### H3C License Management Platform

H3C License Management Platform provides
product licensing services for H3C customers. You can access this system to
obtain an activation file, transfer licenses, or remove the device and license
binding for an uninstalled license.

H3C License Management Platform is
accessible at <http://www.h3c.com/en/License/>.

#### Software license certificate

A software license certificate allows users
to use a license-based feature. It contains license key, license capacity, and
other information.

#### License key

A license key uniquely identifies a
license. 

·     To obtain a formal license key, purchase a
software license certificate. The authorization serial number in the software
license certificate is the license key.

·     To obtain a trial license key, contact your H3C
sales representative or H3C technical support. Support for trial licenses
depends on the device model. For more information, see the license matrixes
document for the device. 

#### Device serial number

A device serial number (SN or S/N) is a
barcode that uniquely identifies a device. It comes with the device and must be
provided when you request a license in H3C License Management Platform.

#### Device ID (DID) and DID file

A DID is a string of characters that
uniquely identifies a hardware device. A DID file stores the DID and other
information. The device comes with a DID or DID file. You must provide the DID
or DID file when you request a license for the device on H3C License Management
Platform.

#### Activation file

An activation file binds a license to a
system.

To use a license-based feature on a system,
you must perform the following tasks:

**1\.**Use the license key and the system’s SN and
DID information to obtain an activation file from H3C License Management
Platform.

**2\.**Install the activation file on the system.

#### Uninstall key and Uninstall file

When you uninstall a license, an Uninstall
file that contains an Uninstall key is created. The Uninstall key is required
for transferring the license.

#### License storage

License storage is a
persistent storage of fixed size for storing licensing information. This
information includes the licensing state, validity period, Uninstall key or
Uninstall file, and other related information.

Data in the license storage persists
through reboot. This ensures licensing accuracy and continuity.

## Restrictions and guidelines: License management

### Management operation restrictions

·     Purchase licenses from H3C official channels.

·     For licenses that have been installed on the
device, execute the display license command
to view the license validity period. To use a license-based feature
continuously, install a new license for the feature before the old license expires.

·     Licenses are typically device locked. To ensure
a successful licensing, use the following licensing guidelines:

**a.**When you purchase a license certificate,
verify the following items:

\-     Make sure the license is compatible with the target device.

\-     Make sure its licensed functionality and capacity meet your
requirements.

**b.**When you register the license, make sure the
provided license key and hardware information are correct.

**c.**Install the activation file on the correct
target device.

·     Make sure no one else is performing license
management tasks while you are managing licenses on the device.

### File operation restrictions

When you manage DID files, activation
files, or Uninstall files, follow these restrictions and guidelines:

·     To avoid licensing error, do not modify the name
of a DID file, activation file, or Uninstall file, or edit the file content.

·     Before you install an activation file, download
the activation file to the storage media of the device such as flash memory.
When installing an activation file, the device automatically copies the
activation file to the license folder in the root directory of the storage
media. The license folder stores important files
for licensing. For licensed features to function correctly, do not delete or
modify the license folder or the files in the license folder.

## Licensing method

The system supports local licensing and
remote licensing.

·     With local licensing, you install and manage
licenses on the device. 

·     With remote licensing, you install and manage
licenses on a license server, which allocates licenses to its license clients
(nodes on the network).

Table 1 Licensing methods

| Licensing method | License installation location | Licensed location | Applicable scenario || Local licensing | Local device | Local device | Applicable to small-sized networks.  You must activate licenses device by device.  ‌ || Remote licensing | H3C license server | License clients | Applicable to large-sized networks.  You activate licenses on the license server, which then automatically assigns licenses to its clients either proactively or on request.    ‌ |


| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

