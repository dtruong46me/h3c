
# Using Python

## About Python

Comware 7 provides a built-in Python
interpreter. You can use Python to perform the following tasks:

·     Execute Python scripts to implement automatic
device configuration.

·     Enter Python shell to configure the device by
using the following items:

¡     Python
2.7 commands.

¡     Python
2.7 standard API.

¡     Extended
API. For more information about the extended API, see "[Comware 7
extended Python API](#_Ref378146100)."

## Executing a Python script

To execute a Python script, use the
following command in user view:

python filename

## Entering the Python shell

To enter the Python shell from user view,
execute the following command:

python 

## Importing and using the extended Python API

To use the extended Python API, you must first
import the API to Python.

### Importing the entire extended API and using the API

#### Procedure

**1\.**Enter the Python shell from user view.

python

**2\.**Import the entire extended API.

import platformtools

**3\.**Execute an extended API function.

platformtools.api

#### Example

\# Use extended API function Transfer to download the test.cfg file from TFTP
server 192.168.1.26.

\<Sysname\> python

Python 2.7.18 (default)

\[GCC 4.4.1] on linux2

Type "help",
"copyright", "credits" or "license" for more
information.

\>\>\> import platformtools

\>\>\> platformtools.Transfer('tftp',
'192.168.1.26', 'test.cfg', 'flash:/test.cfg', user\='', password\='')

\<platformtools.Transfer object at
0xb7eab0e0\>

### Importing an extended API function and using the function

#### Procedure

**1\.**Enter the Python shell from user view.

python

**2\.**Import an extended API function.

from platformtools import api-name

**3\.**Execute an extended API function.

api-function

#### Example

\# Use extended API function Transfer to download the test.cfg file from TFTP
server 192.168.1.26.

\<Sysname\> python

Python 2.7.18 (default)

\[GCC 4.4.1] on linux2

Type "help",
"copyright", "credits" or "license" for more
information.

\>\>\> from platformtools
import Transfer

\>\>\> Transfer('tftp',
'192.168.1.26', 'test.cfg', 'flash:/test.cfg', user\='', password\='')

\<platformtools.Transfer object at
0xb7e5e0e0\>

## Exiting the Python shell

To exit the Python shell, execute the
following command in the Python shell.

exit()

## Python usage examples

### Example: Using a Python script for device configuration

#### Network configuration

Use a Python script to perform the
following tasks:

·     Download configuration files main.cfg and backup.cfg to the
device. 

·     Configure the files as the main and backup
configuration files for the next startup.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704219_x_Img_x_png_0_2215964_294551_0.png)

 

#### Procedure

\# Use a text editor on the PC to configure Python
script test.py as follows:

#!usr/bin/python

import platformtools

 

platformtools.Transfer('tftp',
'192.168.1.26', 'main.cfg', 'flash:/main.cfg')

platformtools.Transfer('tftp',
'192.168.1.26', 'backup.cfg', 'flash:/backup.cfg')

platformtools.CLI('startup
saved-configuration flash:/main.cfg main ;startup saved-configuration
flash:/backup.cfg backup')

\# Use TFTP to download the script to the
device.

\<Sysname\> tftp 192.168.1.26 get
test.py

\# Execute the script. 

\<Sysname\> python flash:/test.py

\<Sysname\>startup
saved-configuration flash:/main.cfg main

Please wait...... Done.

\<Sysname\>startup
saved-configuration flash:/backup.cfg backup

Please wait...... Done.

#### Verifying the configuration

\# Display startup configuration files.

\<Sysname\> display startup

 Current startup saved-configuration
file: flash:/startup.cfg

 Next main startup
saved-configuration file: flash:/main.cfg

 Next backup startup
saved-configuration file: flash:/backup.cfg

