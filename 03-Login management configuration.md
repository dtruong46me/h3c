
# Login overview

The device supports the following types
of login methods:

·     CLI login—At the CLI, you can enter text commands to configure and manage the
device. 

To log in to the CLI, you can use one of
the following methods:

¡     Connect
to the console port.

¡     Use
Telnet.

¡     Use
SSH.

·     Web login—Through the Web interface, you can configure and manage the device visually.

·     SNMP access—You can run SNMP on an NMS to access the device MIB, and perform Get
and Set operations to configure and manage the device.

·     RESTful access—You can use RESTful API operations to configure and manage the
device.

The first time you access the device, you can
only log in to the CLI through the console port unless the device is automatically
configured at startup. After login, you can change console login parameters or configure
other access methods.

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode and non-FIPS mode. For more information
about FIPS mode, see Security Configuration Guide.

Telnet, HTTP-based Web login, and
HTTP-based RESTful access are not supported in FIPS mode.

In login management related descriptions,
it is assumed that the device does not enter the automatic configuration
process at startup.

