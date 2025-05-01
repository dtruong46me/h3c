
# Configuring keychains

## About keychains

A keychain, a sequence of keys, provides
dynamic authentication to ensure secure communication by periodically changing
the key and authentication algorithm without service interruption.

A keychain operates in absolute time mode.
In this mode, each time point during a key's lifetime is the UTC time and is
not affected by the system's time zone or daylight saving time.

Each key in a keychain has a key string,
authentication algorithm, sending lifetime, and receiving lifetime. When the
system time is within the lifetime of a key in a keychain, an application uses
the key to authenticate incoming and outgoing packets. The keys in the keychain
take effect one by one according to the sequence of the configured lifetimes.
In this way, the authentication algorithms and keys are dynamically changed to
implement dynamic authentication.

## Restrictions and guidelines: Keychain configuration

To make sure only one key in a keychain is
used at a time to authenticate packets to a peer, set non-overlapping sending
lifetimes for the keys in the keychain.

The keys used by the local device and the
peer device must have the same authentication algorithm and key string.

## Configuring a keychain

**1\.**Enter system view.

system-view

**2\.**Create a keychain and enter keychain view.

keychain keychain-name mode absolute

**3\.**(Optional.) Configure TCP authentication.

¡     Set
the kind value in the TCP Enhanced Authentication Option.

tcp-kind kind-value

By default, the kind value is 254\.

¡     Set
an algorithm ID for a TCP authentication algorithm.

tcp-algorithm-id { hmac-md5 \| hamc-sm3 \| md5 \| sm3 } algorithm-id

By default, the algorithm ID is 3 for the
MD5 authentication algorithm, 51 for the SM3 authentication algorithm, 52 for
the HMAC-SM3 authentication algorithm, and 5 for the HMAC-MD5 authentication
algorithm.

When the local device uses TCP to
communicate with a peer device from another vendor, make sure both devices have
the same kind value and algorithm ID settings. If they do not, modify the settings
on the local device.

**4\.**(Optional.) Set a tolerance time for accept
keys in the keychain.

accept-tolerance { value \| infinite }

By default, no tolerance time is configured
for accept keys in a keychain.

If authentication information is changed,
information mismatch occurs on the local and peer devices, and the service
might be interrupted. Use this command to ensure continuous packet
authentication.

**5\.**Create a key and enter key view.

key key-id

**6\.**Configure the key.

¡     Specify
an authentication algorithm for the key.

authentication-algorithm { hmac-md5 \| hmac-sha-256 \| hmac-sm3 \| md5\| sm3 }

By default, no authentication algorithm
is specified for a key.

¡     Configure
a key string for the key.

key-string { cipher \| plain } string

By default, no key string is configured.

¡     Set
the sending lifetime in UTC mode for the key.

send-lifetime utc start-time start-date { duration { duration-value \| infinite } \| to end-time end-date }

By default, the sending lifetime is not
configured for a key.

¡     Set
the receiving lifetime in UTC mode for the key.

accept-lifetime utc start-time start-date { duration { duration-value \| infinite } \| to end-time end-date }

By default, the receiving lifetime is not
configured for a key.

¡     (Optional.)
Specify the key as the default send key.

default-send-key

By default, no default send key is
configured for a keychain.

You can specify only one key as the default
send key in a keychain.

## Display and maintenance commands for keychain

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display keychain information. | display keychain \[ name keychain-name \[ key key-id ] ] |

 

## Keychain configuration example

### Example: Configuring keychains

#### Network configuration

As shown in [Figure 1](#_Ref499298648),
establish an OSPF neighbor relationship between Switch A and Switch B, and use
a keychain to authenticate packets between the switches. Configure key 1 and
key 2 for the keychain and make sure key 2 is used immediately when key 1
expires.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705409_x_Img_x_png_0_2216071_294551_0.png)

 

#### Procedure

**1\.**Configure Switch A:

\# Configure IP addresses for interfaces.
(Details not shown.)

\# Configure OSPF.

\<SwitchA\> system-view

\[SwitchA] ospf 1 router-id
1.1.1.1

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0]
network 192.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] quit

\[SwitchA-ospf-1] quit

\# Create a keychain named abc, and specify
the absolute time mode for it.

\[SwitchA] keychain abc mode
absolute

\# Create key 1 for keychain abc, specify an
authentication algorithm, and configure a key string and the sending and receiving
lifetimes for the key.

\[SwitchA-keychain-abc] key 1

\[SwitchA-keychain-abc-key-1] authentication-algorithm
md5

\[SwitchA-keychain-abc-key-1] key-string
plain 123456

\[SwitchA-keychain-abc-key-1] send-lifetime
utc 10:00:00 2015/02/06 to 11:00:00 2015/02/06

\[SwitchA-keychain-abc-key-1]
accept-lifetime utc 10:00:00 2015/02/06 to 11:00:00 2015/02/06

\[SwitchA-keychain-abc-key-1]
quit

\# Create key 2 for keychain abc, specify an
authentication algorithm, and configure a key string and the sending and
receiving lifetimes for the key.

\[SwitchA-keychain-abc] key 2

\[SwitchA-keychain-abc-key-2] authentication-algorithm
hmac-md5

\[SwitchA-keychain-abc-key-2] key-string
plain pwd123

\[SwitchA-keychain-abc-key-2] send-lifetime
utc 11:00:00 2015/02/06 to 12:00:00 2015/02/06

\[SwitchA-keychain-abc-key-2]
accept-lifetime utc 11:00:00 2015/02/06 to 12:00:00 2015/02/06

\[SwitchA-keychain-abc-key-2]
quit

\[SwitchA-keychain-abc] quit

\# Configure VLAN-interface 100 to use
keychain abc for authentication.

\[SwitchA] interface vlan-interface
100

\[SwitchA-Vlan-interface100]
ospf authentication-mode keychain abc

\[SwitchA-Vlan-interface100] quit

**2\.**Configure Switch B:

\# Configure IP addresses for interfaces.
(Details not shown.)

\# Configure OSPF.

\[SwitchB] ospf 1 router-id 2.2.2.2

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0]
network 192.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0]
quit

\[SwitchB-ospf-1] quit

\# Create a keychain named abc, and specify
the absolute time mode for it.

\[SwitchB] keychain abc mode
absolute

\# Create key 1 for keychain abc, specify an
authentication algorithm, and configure a key string and the sending and
receiving lifetimes for the key.

\[SwitchB-keychain-abc] key 1

\[SwitchB-keychain-abc-key-1]
authentication-algorithm md5

\[SwitchB-keychain-abc-key-1]
key-string plain 123456

\[SwitchB-keychain-abc-key-1]
send-lifetime utc 10:00:00 2015/02/06 to 11:00:00 2015/02/06

\[SwitchB-keychain-abc-key-1]
accept-lifetime utc 10:00:00 2015/02/06 to 11:00:00 2015/02/06

\[SwitchB-keychain-abc-key-1]
quit

\# Create key 2 for keychain abc, specify an
authentication algorithm, and configure a key string and the sending and
receiving lifetimes for the key.

\[SwitchB-keychain-abc] key 2

\[SwitchB-keychain-abc-key-2]
authentication-algorithm hmac-md5

\[SwitchB-keychain-abc-key-2] key-string
plain pwd123

\[SwitchB-keychain-abc-key-2]
send-lifetime utc 11:00:00 2015/02/06 to 12:00:00 2015/02/06

\[SwitchB-keychain-abc-key-2]
accept-lifetime utc 11:00:00 2015/02/06 to 12:00:00 2015/02/06

\[SwitchB-keychain-abc-key-2]
quit

\[SwitchB-keychain-abc] quit

\# Configure VLAN-interface 100 to use
keychain abc for authentication.

\[SwitchB] interface vlan-interface
100

\[SwitchB-Vlan-interface100]
ospf authentication-mode keychain abc

\[SwitchB-Vlan-interface100]
quit

#### Verifying the configuration

**1\.**When the system time is within the lifetime
from 10:00:00 to 11:00:00 on the day 2015/02/06, verify the status of the keys
in keychain abc.

\# Display keychain information on Switch
A. The output shows that key 1 is the valid key.

\[SwitchA] display keychain

 

 Keychain name          : abc

   Mode                 :
absolute

   Accept tolerance     : 0

   TCP kind value       : 254

   TCP algorithm value

     HMAC-MD5           : 5

     MD5                : 3

     HMAC-SM3           : 52

     SM3                : 51

   Default send key ID  : None

   Active send key ID   : 1

   Active accept key IDs: 1

 

   Key ID               : 1

     Key string         :
$c$3$dYTC8QeOKJkwFwP2k/rWL\+1p6uMTw3MqNg\=\=

     Algorithm          : md5

     Send lifetime      :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Send status        : Active

     Accept lifetime    :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Accept status      : Active

 

   Key ID               : 2

     Key string         :
$c$3$7TSPbUxoP1ytOqkdcJ3K3x0BnXEWl4mOEw\=\=

     Algorithm          :
hmac-md5

     Send lifetime      : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Send status        :
Inactive

     Accept lifetime    : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Accept status      :
Inactive

\# Display keychain information on Switch
B. The output shows that key 1 is the valid key.

\[SwitchB] display keychain

 

 Keychain name          : abc

   Mode                 :
absolute

   Accept tolerance     : 0

   TCP kind value       : 254

   TCP algorithm value

     HMAC-MD5           : 5

     MD5                : 3

     HMAC-SM3           : 52

     SM3                : 51

   Default send key ID  : None

   Active send key ID   : 1

   Active accept key IDs: 1

 

   Key ID               : 1

     Key string         :
$c$3$/G/Shnh6heXWprlSQy/XDmftHa2JZJBSgg\=\=

     Algorithm          : md5

     Send lifetime      :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Send status        : Active

     Accept lifetime    : 10:00:00
2015/02/06 to 11:00:00 2015/02/06

     Accept status      : Active

 

   Key ID               : 2

     Key string         :
$c$3$t4qHAw1hpZYN0JKIEpXPcMFMVT81u0hiOw\=\=

     Algorithm          :
hmac-md5

     Send lifetime      : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Send status        :
Inactive

     Accept lifetime    : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Accept status      :
Inactive

**2\.**When the system time is within the lifetime
from 11:00:00 to 12:00:00 on the day 2015/02/06, verify the status of the keys
in keychain abc.

\# Display keychain information on Switch
A. The output shows that key 2 becomes the valid key.

\[SwitchA] display keychain

 

 Keychain name          : abc

   Mode                 :
absolute

   Accept tolerance     : 0

   TCP kind value       : 254

   TCP algorithm value

     HMAC-MD5           : 5

     MD5                : 3

     HMAC-SM3           : 52

     SM3                : 51

   Default send key ID  : None

   Active send key ID   : 2

   Active accept key IDs: 2

 

   Key ID               : 1

     Key string         :
$c$3$dYTC8QeOKJkwFwP2k/rWL\+1p6uMTw3MqNg\=\=

     Algorithm          : md5

     Send lifetime      :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Send status        :
Inactive

     Accept lifetime    :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Accept status      :
Inactive

 

   Key ID               : 2

     Key string         :
$c$3$7TSPbUxoP1ytOqkdcJ3K3x0BnXEWl4mOEw\=\=

     Algorithm          :
hmac-md5

     Send lifetime      : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Send status        : Active

     Accept lifetime    : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Accept status      : Active

\# Display keychain information on Switch
B. The output shows that key 2 becomes the valid key.

\[SwitchB] display keychain

 

 Keychain name          : abc

   Mode                 :
absolute

   Accept tolerance     : 0

   TCP kind value       : 254

   TCP algorithm value

     HMAC-MD5           : 5

     MD5                : 3

     HMAC-SM3           : 52

     SM3                : 51

   Default send key ID  : None

   Active send key ID   : 1

   Active accept key IDs: 1

 

   Key ID               : 1

     Key string         :
$c$3$/G/Shnh6heXWprlSQy/XDmftHa2JZJBSgg\=\=

     Algorithm          : md5

     Send lifetime      :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Send status        :
Inactive

     Accept lifetime    :
10:00:00 2015/02/06 to 11:00:00 2015/02/06

     Accept status      :
Inactive

 

   Key ID               : 2

     Key string         :
$c$3$t4qHAw1hpZYN0JKIEpXPcMFMVT81u0hiOw\=\=

     Algorithm          :
hmac-md5

     Send lifetime      : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Send status        : Active

     Accept lifetime    : 11:00:00
2015/02/06 to 12:00:00 2015/02/06

     Accept status      : Active

 

