
# Configuring user profiles

## About user profiles

A user profile defines
a set of parameters, such as a QoS policy, for a user or a class of users. A
user profile can be reused when a user connected to the network on a different
interface.

The user profile application allows flexible
traffic policing on a per-user basis. Each time a user passes authentication, the
server sends the device the name of the user profile specified for the user.
The device applies the parameters in the user profile to the user.

User profiles are typically used for
resource allocation per user. For example, the interface-based traffic policing
limits the total amount of bandwidth available to a group of users. However,
user-profile-based traffic policing can limit the amount of bandwidth available
to a single user.

## Prerequisites for user profile

A user profile works with authentication
methods. You must configure authentication for a user profile. For information
about supported authentication methods, see the configuration guides for the
related authentication modules.

## Configuring a user profile

**1\.**Enter system view.

system-view

**2\.**Create a user profile and enter user profile
view.

user-profile profile-name

**3\.**Configure the user profile. Choose the
options to configure as needed:

¡     Apply
an existing QoS policy to the user profile.

qos apply policy policy-name { inbound \| outbound }

By default, no QoS policy is applied to a
user profile.

¡     Configure
a CAR policy for the user profile.

qos car { inbound \| outbound } any cir committed-information-rate \[ cbs committed-burst-size \[ ebs excess-burst-size ] ]

qos car { inbound \| outbound } any cir committed-information-rate \[ cbs committed-burst-size
] pir peak-information-rate \[ ebs excess-burst-size ]

By default, no CAR policy is configured
for a user profile.

For more information about QoS policies
and CAR policies, see ACL and QoS Configuration Guide.


## Display and maintenance commands for user profiles

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display configuration and online user information for the specified user profile or all user profiles. | display user-profile \[ name profile-name ] \[ slot slot-number ] |

 

## User profile configuration examples

### Example: Configuring user profiles and QoS policies

#### Network requirements

As shown in [Figure 1](#_Ref453763929), the
device performs local 802.1X authentication on the users in domain user for authentication and authorization efficiency.

Configure user profiles and QoS policies on
the device to meet the following requirements:

·     User A cannot access the Internet during 8: 30
and 12:00 every day even if User A passes 802.1X authentication.

·     User B has an upload speed of 2 Mbps after
passing 802.1X authentication.

·     User C has a download speed of 4 Mbps after
passing 802.1X authentication.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705405_x_Img_x_png_0_2216069_294551_0.png)

‌

#### Configuration procedure

**1\.**Configure a QoS policy for User A:

\# Create a periodic time range from 8:30 to
12:00 every day.

\<Device\> system-view

\[Device] time-range for\_usera
8:30 to 12:00 daily

\# Create IPv4 basic ACL 2000, and
configure a rule to match all packets during the time range for\_usera.

\[Device] acl basic 2000

\[Device-acl-basic-2000] rule
permit time-range for\_usera

\[Device-acl-basic-2000] quit

\# Create a traffic class named for\_usera, and
use ACL 2000 as the match criterion.

\[Device] traffic classifier
for\_usera

\[Device-classifier-for\_usera]
if-match acl 2000

\[Device-classifier-for\_usera]
quit

\# Create a traffic behavior named for\_usera, and
configure the deny action.

\[Device] traffic behavior
for\_usera

\[Device-behavior-for\_usera]
filter deny

\[Device-behavior-for\_usera]
quit

\# Create a QoS policy named for\_usera, and
associate traffic class for\_usera and traffic behavior for\_usera in the QoS policy.

\[Device] qos policy for\_usera

\[Device-qospolicy-for\_usera]
classifier for\_usera behavior for\_usera

\[Device-qospolicy-for\_usera]
quit

**2\.**Create a user profile for User A and apply the
QoS policy to the user profile:

\# Create a user profile named usera.

\[Device] user-profile usera

\# Apply QoS policy for\_usera to the
inbound direction of user profile usera.

\[\[Device-user-profile-usera]
qos apply policy for\_usera inbound

\[Device-user-profile-usera]
quit

**3\.**Configure a QoS policy for limiting the
traffic rate for User B:

\# Create a traffic class named class to match
all packets.

\[Device] traffic classifier
class

\[Device-classifier-class]
if-match any

\[Device-classifier-class] quit

\# Create a traffic behavior named for\_userb, and
configure a traffic policing action (CIR 2000 kbps).

\[Device] traffic behavior
for\_userb

\[Device-behavior-for\_userb]
car cir 2000

\[Device-behavior-for\_userb]
quit

\# Create a QoS policy named for\_userb, and
associate traffic class class and traffic behavior for\_userb in the QoS policy.

\[Device] qos policy for\_userb

\[Device-qospolicy-for\_userb]
classifier class behavior for\_userb

\[Device-qospolicy-for\_userb]
quit

**4\.**Create a user profile for User B and apply the
QoS policy to the user profile:

\# Create a user profile named userb.

\[Device] user-profile userb

\# Apply QoS policy for\_userb to the
inbound direction of user profile userb.

\[Device-user-profile-userb]
qos apply policy for\_userb inbound

\[Device-user-profile-userb]
quit

**5\.**Configure a QoS policy for limiting the
traffic rate for User C:

\# Create a traffic behavior named for\_userc, and
configure a traffic policing action (CIR 4000 kbps).

\[Device] traffic behavior
for\_userc

\[Device-behavior-for\_userc]
car cir 4000

\[Device-behavior-for\_userc]
quit

\# Create a QoS policy named for\_userc, and
associate traffic class class and traffic behavior for\_userc in the QoS policy.

\[Device] qos policy for\_userc

\[Device-qospolicy-for\_userc]
classifier class behavior for\_userc

\[Device-qospolicy-for\_userc]
quit

**6\.**Create a user profile for User C and apply the
QoS policy to the user profile:

\# Create a user profile named userc.

\[Device] user-profile userc

\# Apply QoS policy for\_userc to the
outbound direction of user profile userc.

\[Device-user-profile-userc]
qos apply policy for\_userc outbound

\[Device-user-profile-userc]
quit

**7\.**Configure local users:

\# Create a local user named usera.

\[Device] local-user usera
class network

New local user added.

\# Set the password to 123456TESTusera\&! for user usera.

\[Device-luser-network-usera]
password simple 123456TESTusera\&!

\# Authorize user usera to use the
LAN access service.

\[Device-luser-network-usera]
service-type lan-access

\# Specify user profile usera as the authorization
user profile for user usera.

\[Device-luser-network-usera]
authorization-attribute user-profile usera

\[Device-luser-network-usera]
quit

\# Create a local user named userb.

\[Device] local-user userb
class network

New local user added.

\# Set the password to 123456TESTuserb\&! for user userb.

\[Device-luser-network-userb]
password simple 123456TESTuserb\&!

\# Authorize user userb to use the
LAN access service.

\[Device-luser-network-userb]
service-type lan-access

\# Specify user profile userb as the authorization
user profile for user userb.

\[Device-luser-network-userb]
authorization-attribute user-profile userb

\[Device-luser-network-userb]
quit

\# Create a local user named userc.

\[Device] local-user userc
class network

New local user added.

\# Set the password to 123456TESTuserc\&! for user userc.

\[Device-luser-network-userc]
password simple 123456TESTuserc\&!

\# Authorize user userc to use the
LAN access service.

\[Device-luser-network-userc]
service-type lan-access

\# Specify user profile userc as the authorization
user profile for user userc.

\[Device-luser-network-userc]
authorization-attribute user-profile userc

\[Device-luser-network-userc]
quit

**8\.**Configure the authentication, authorization,
and accounting methods for local users:

\[Device] domain user

\[Device-isp-user]
authentication lan-access local

\[Device-isp-user]
authorization lan-access local

\[Device-isp-user] accounting
login none

\[Device-isp-user] quit

**9\.**Configure 802.1X:

\# Enable 802.1X on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] dot1x

\# Enable MAC-based access control on the
port. By default, a port uses MAC-based access control.

\[Device-Ten-GigabitEthernet1/0/1] dot1x port-method
macbased

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Enable 802.1X globally.

\[Device] dot1x

#### Verifying the configuration

\# Verify that the three users can pass 802.1X
authentication and that QoS policies take effect on these users. (Details not
shown.)

\# Display user profile information.

\<Device\> display user-profile

  User-Profile: usera

    Inbound:

      Policy: for\_usera

 

    slot 1:

      User -:

        Authentication type: 802.1X

        Network attributes:

          Interface    : Ten-GigabitEthernet1/0/1

          MAC address  :
6805-ca06-557b

          Service VLAN : 1

 

  User-Profile: userb

    Inbound:

      Policy: for\_userb

 

    slot 1:

      User -:

        Authentication type: 802.1X

        Network attributes:

          Interface    : Ten-GigabitEthernet1/0/1

          MAC address  :
80c1-6ee0-2664

          Service VLAN : 1

 

  User-Profile: userc

    Outbound:

      Policy: for\_userc

 

    slot 1:

      User -:

        Authentication type: 802.1X

        Network attributes:

          Interface    : Ten-GigabitEthernet1/0/1

          MAC address  :
6805-ca05-3efa

          Service VLAN : 1

 

