
# Configuring password control

## About password control

Password control
allows you to implement the following features:

·     Manage login and super password setup, expirations,
and updates for local users.

·     Control user login status based on predefined
policies.

For more information about local users, see
"Configuring AAA." For information about super passwords, see RBAC in
Fundamentals Configuration Guide.

### Password setting

#### Minimum password length

You can define the minimum length of user
passwords. The system rejects the setting of a password that is shorter than
the configured minimum length.

#### Password composition policy

A password can be a combination of
characters from the following types:

·     Uppercase letters A to Z.

·     Lowercase letters a to z.

·     Digits 0 to 9\.

·     Special characters in [Table 1](#_Ref427653337). 

Table 1 Special Characters

| Character name | Symbol | Character name | Symbol |
| --- | --- | --- | --- |
| Ampersand sign | \& | Apostrophe | ' || Asterisk | \* | At sign | @ || Back quote | \` | Back slash | \\ || Blank space | N/A | Caret | ^ || Colon | : | Comma | , || Dollar sign | $ | Dot | . || Equal sign | \= | Exclamation point | ! || Left angle bracket | \< | Left brace | { || Left bracket | \[ | Left parenthesis | ( || Minus sign | \- | Percent sign | % || Plus sign | \+ | Pound sign | \# || Quotation marks | " | Right angle bracket | \> || Right brace | } | Right bracket | ] || Right parenthesis | ) | Semi-colon | ; || Slash | / | Tilde | \~ || Underscore | \_ | Vertical bar | \| |
















 

Depending on the system's security
requirements, you can set the minimum number of character types a password must
contain and the minimum number of characters for each type, as shown in [Table 2](#_Ref360007384). 

Table 2 Password composition policy

| Password combination level | Minimum number of character types | Minimum number of characters for each type |
| --- | --- | --- |
| Level 1 | One | One || Level 2 | Two | One || Level 3 | Three | One || Level 4 | Four | One |




 

When a user sets or changes a password, the
system checks if the password meets the combination requirement. If it does not,
the operation fails.

#### Password complexity checking policy

A less complicated password is more likely
to be cracked, such as a password containing the username or repeated
characters. For higher security, you can configure a password complexity
checking policy to ensure that all user passwords are relatively complicated. When
a user configures a password, the system checks the complexity of the password.
If the password is complexity-incompliant, the configuration will fail. 

You can apply the following password
complexity requirements:

·     A password cannot contain the username, the
reverse of the username, or part of the username or the reverse of the username.
For example, if the username is abc, a password
such as abc982, 2cba, ab, or cb is not complex
enough.

·     A minimum of three identical consecutive characters
is not allowed. For example, password a111 is not
complex enough.

### Password updating and expiration

#### Password updating

This feature allows you to set the minimum
interval at which users can change their passwords. A user can only change the
password once within the specified interval.

The minimum interval does not apply to the
following situations:

·     A user is prompted to change the password at
first login.

·     The password aging time expires.

#### Password expiration

Password expiration imposes a lifecycle on
a user password. After the password expires, the user needs to change the
password.

The system displays an error message for a
login attempt with an expired password. The user is asked to provide a new
password. The new password must be valid, and the user must enter exactly the
same password when confirming it. 

Telnet users, SSH users, and console users
can change their own passwords. FTP users must have their passwords changed by
the administrator.

#### Early notice on pending password expiration

When a user logs in, the system checks
whether the password will expire in a time equal to or less than the specified notification
period. If so, the system notifies the user when the password will expire and
provides a choice for the user to change the password. 

·     If the user sets a new valid password, the
system records the new password and the setup time. 

·     If the user does not or fails to change the
password, the system allows the user to log in by using the current password
until the password expires.

Telnet users, SSH users, and console users
can change their own passwords. FTP users must have their passwords changed by
the administrator.

#### Login with an expired password

You can allow a user to log in a certain
number of times within a period of time after the password expires. For
example, if you set the maximum number of logins with an expired password to 3 and
the time period to 15 days, a user can log in three times within 15 days after
the password expires.

#### Password history

This feature allows the system to store passwords
that a user has used. 

When a network access user changes the password,
the system compares the new password with the current password and those stored
in the password history records. The new password must be different from the
current one and those stored in the history records by a minimum of four different
characters. Otherwise, the system will display an error message, and the new password
is not successfully set.

The local passwords and super passwords for
device management users are stored in hash form and cannot be converted to
plain texts. When a device management user changes a local password or super
password, follow these rules:

·     If the new password is set by using the hash
method, the system will not compare the new password with the current one and
those stored in the history password records.

·     If the new password is set in plain text, the
system compares the new password with the current password and those stored in
the password history records. A new password must be different from those
stored in the history password records. If the current password is required,
the new password must also be different from the current one by a minimum of
four different characters. Otherwise, the system will display an error message,
and the new password is not successfully set.

You can set the maximum number of history
password records for the system to maintain for each user. When the number of
history password records exceeds the setting, the most recent record overwrites
the earliest one.

Current login passwords are not stored in
the password history for device management users. Device management users have
their passwords saved in cipher text, which cannot be recovered to plaintext
passwords.

### User login control

#### First login

By default, if the global password control feature
is enabled, users must change the password at first login before they can
access the system. In this situation, password changes are not subject to the
minimum password update interval. If it is not necessary for users to change
the password at first login, disable the password change at first login feature.

#### Password control blacklist

The password control blacklist prevents
abnormal users from logging in by recording the login failures and maintaining
the status of blacklisted user accounts.

If an FTP or VTY user fails to log in, the
system adds the user account and the user's IP address to the password control blacklist.
If an AUX or USB user fails to log in, the system adds the user account to the
password control blacklist.

If a user uses the same user account to log
in to the device from different IP addresses and fails the logins, the device
will create a blacklist entry for each IP address for the user account. When
the maximum number of blacklist entries for the user account is reached, a
blacklist entry for a new IP address of the user account will overwrite the
earliest blacklist entry for the user account.

#### Login attempt limit

Limiting the number of consecutive login failures
can effectively prevent password guessing.

When a user fails the maximum number of consecutive
attempts, login attempt limit limits the user and user account in any of the
following ways:

·     For an FTP or VTY user, the system prohibits the
user from using the user account to log in through the user's IP address. For an
AUX or USB user, the system prohitibs the user from logging in through AUX or
USB user line. The locked user can use their own account to log in to the
device only after the account is manually removed from the password control blacklist.

·     Allows the user to continue using the user account.
The user account is removed from the password control blacklist when the user uses
this account to successfully log in to the device.

·     Locks the user account and the user's IP address
for a period of time. 

The user can use the account to log in when
either of the following conditions exists:

¡     The
locking timer expires.

¡     The account
is manually removed from the password control blacklist before the locking
timer expires.

‌

|  | NOTE:  This account is locked only for the user at the locked IP address. A user from an unlocked IP address can still use this account, and the user at the locked IP address can use other unlocked user accounts. |
| --- | --- |

‌

#### Maximum account idle time

You can set the maximum account idle time for
user accounts. When an account is idle for this period of time since the last
successful login, the account becomes invalid. 

#### Login control with a weak password

The system checks for weak passwords for
Telnet, SSH, HTTP, or HTTPS device management users. A password is weak if it
does not meet the following requirements:

·     Password composition restriction.

·     Minimum password length restriction.

·     Password complexity username checking policy.

By default, the system displays a message
about a weak password but does not force the user to change it. To improve the
device security, you can enable the mandatory weak password change feature,
which forces the users to change the identified weak passwords. The users can
log in to the device only after their passwords meet the password requirements.

### Password not displayed in any form

For security purposes, nothing is displayed
when a user enters a password.

### Logging

The system generates
a log each time a user changes its password successfully or is added to the
password control blacklist because of login failures.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode (see "Configuring FIPS") and
non-FIPS mode.

## Restrictions and guidelines: Password control configuration

| IMPORTANT | IMPORTANT:  To successfully enable the global password control feature and allow device management users to log in to the device, make sure the device have sufficient storage space. |
| --- | --- |

 

The password control features can be
configured in several different views, and different views support different
features. The settings configured in different views or for different objects
have the following application ranges:

·     Settings for super passwords apply only to super
passwords.

·     Settings in local user view apply only to the
password of the local user.

·     Settings in user group view apply to the
passwords of the local users in the user group if you do not configure password
policies for these users in local user view.

·     Global settings in system view apply to the
passwords of the local users in all user groups if you do not configure
password policies for these users in both local user view and user group view.

For local user passwords, the settings with
a smaller application scope have higher priority.

## Password control tasksat a glance

To configure password control, perform the
following tasks:

**1\.**[Enabling password control](#_Ref475089419)

**2\.**(Optional.) [Setting global password control parameters](#_Ref475089441)

**3\.**(Optional.) [Setting user group password control
parameters](#_Ref475089449)

**4\.**(Optional.) [Setting local user password control
parameters](#_Ref475089452)

**5\.**(Optional.) [Setting super password control parameters](#_Ref475089455)

## Enabling password control

#### About this task

For device management users, enabling global
password control is a prerequisite for password control functions to take
effect except for password composition policy, minimum password length, and username
checking. For a specific password restriction setting (password expiration or password
history management) to take effect, make sure you enable both the global
password control feature and the specific password restriction.

For network access users, all password
control functions take effect only after the global password control feature is
enabled.

#### Restrictions and guidelines

After global password control is enabled, follow
these restrictions and guidelines:

·     You cannot display the password and super
password configurations for device management users by using the corresponding display commands. 

·     You cannot display the password configuration
for network access users by using the corresponding display command.

·     The passwords configured for local users must
contain a minimum of four different characters.

·     In FIPS mode, the global password control
feature is enabled for device management users and cannot be disabled for them.

·     To ensure correct function of password control,
configure the device to use NTP to obtain the UTC time. After global password
control is enabled, password control will record the UTC time when the password
is set. The recorded UTC time might not be consistent with the actual UTC time
due to power failure or device reboot. The inconsistency will cause the
password expiration feature to malfunction. For information about NTP, see Network Management and Monitoring Configuration Guide.

·     The device automatically generates a .dat file
and saves the file to the storage media. The file is used to record
authentication and login information of the local users. Do not manually delete
or modify the file.

·     The global password control feature enables the
system to record history passwords. When the number of history password records
of a user reaches the maximum number, the newest history record overwrites the
earliest one. To delete the existing history password records, use one of the following
methods:

¡     Use
the undo
password-control enable command to disable the password
control feature globally.

¡     Use
the reset
password-control history-record command to clear
the passwords manually.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the global password control feature.

In non-FIPS mode:

password-control enable\[network-class ]

By default, the global password control
feature is disabled for device management and network access users.

In FIPS mode:

password-control enable \[ network-class ]

By default, the global password control
feature is enabled for device management users and cannot be disabled. The
global password control feature is disabled for network access users.

**3\.**(Optional.) Enable a password restriction
feature.

password-control { aging \| composition \| history \| length } enable

By default, all four password restriction
features are enabled.

## Setting global password control parameters

#### Restrictions and guidelines

The global password control parameters in
system view apply to all device management and network access local users.

You can configure all password control
features for device management users. The password aging time, minimum password
length, password complexity policy, password composition policy, and user login
attempt limit can be configured in system view, user group view, and local user
view. 

You can configure only the following
password control features for network access users: 

·     Minimum password length.

·     Password complexity policy.

·     Password composition policy.

·     Minimum password update interval.

·     Maximum number of history password records for each
user. 

Where, the minimum password length,
password complexity policy, and password composition policy can be configured in
system view, user group view, and local user view.

The password settings with a smaller
application scope have higher priority. For local users, password settings
configured in local user view have the highest priority, and global settings in
system view have the lowest priority.

The password-control login-attempt command takes effect immediately and can affect the users already in
the password control blacklist. Other password control configurations do not take
effect on users that have been logged in or passwords that have been
configured.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure password settings.

¡     Set
the minimum password length.

In non-FIPS mode:

password-control length length

The default setting is 10 characters. 

In FIPS mode:

password-control length length

The default length is 15 characters.

¡     Configure
the password composition policy.

In non-FIPS mode:

password-control composition type-number type-number \[ type-length type-length ]

By default, a password must contain a
minimum of two character types and a minimum of one character for each type. 

In FIPS mode:

password-control composition type-number type-number \[ type-length type-length ]

By default, a password must contain a
minimum of four character types and a minimum of one character for each type.

¡     Configure
the password complexity checking policy.

password-control complexity { same-character
\| user-name } check

In non-FIPS mode:

By default, username checking is enabled
but repeated character checking is disabled.

In FIPS mode:

By default, the system does not perform
password complexity checking.

¡     Set
the maximum number of history password records for each user.

password-control history max-record-number

The default setting is 4\.

**3\.**Configure password updating and expiration.

¡     Set
the minimum password update interval.

password-control update interval interval

The default setting is 24 hours.

¡     Set
the password aging time.

password-control aging aging-time

The default setting is 90 days.

¡     Set
the number of days during which a user is notified of the pending password
expiration.

password-control alert-before-expire alert-time

The default setting is 7 days.

¡     Set
the maximum number of days and maximum number of times that a user can log in
after the password expires.

password-control expired-user-login delay delay times times

By default, a user can log in three times
within 30 days after the password expires.

**4\.**Configure user login control.

¡     Enable
the password control blacklist feature for all user line types.

password-control blacklist all-line

By default, the password control blacklist
feature is disabled for all user line types. The password control blacklist
feature is enabled only for FTP and VTY users when you enable the global
password control feature.

¡     Set
the maximum number of blacklist entries for a user account.

password-control per-user blacklist-limit max-number

By default, the maximum number of
blacklist entries for a user account is 32\.

¡     Configure
the login attempt limit.

password-control login-attempt login-times \[ exceed { lock \| lock-time time \| unlock } ]

By default, the maximum number of login
attempts is 3 and a user failing to log in after the specified number of
attempts must wait for 1 minute before trying again.

¡     Set
the maximum account idle time.

password-control login idle-time idle-time

The default setting is 90 days.

If a user account is idle for this period
of time, the account becomes invalid and can no longer be used to log in to the
device. To disable the account idle time restriction, set the idle time value
to 0\.

¡     Set
the user authentication timeout time.

password-control authentication-timeout timeout

The default setting is 600 seconds.

This command takes effect only on Telnet
and terminal users.

¡     Disable
password change at first login.

undo password-control change-password first-login
enable

By default, the password change at first
login feature is enabled.

In FIPS mode, the password change at
first login feature cannot be disabled.

¡     Enable
mandatory weak password change.

password-control change-password weak-password enable

In non-FIPS mode:

By default, the mandatory weak password
change feature is disabled.

In FIPS mode:

By default, the mandatory weak password
change feature is enabled, and cannot be disabled.

## Setting user group password control parameters

**1\.**Enter system view.

system-view

**2\.**Create a user group and enter its view.

user-group group-name

For information about how to configure a
user group, see "Configuring AAA."

**3\.**Configure the password aging time for the
user group.

password-control aging aging-time

By default, the password aging time of
the user group equals the global password aging time.

**4\.**Configure the minimum password length for
the user group.

password-control length length

By default, the minimum password length of
the user group equals the global minimum password length.

**5\.**Configure the password composition policy
for the user group.

password-control composition type-number type-number \[ type-length type-length ]

By default, the password composition
policy of the user group equals the global password composition policy.

**6\.**Configure the password complexity checking
policy for the user group.

password-control complexity { same-character
\| user-name } check

By default, the password complexity
checking policy of the user group equals the global password complexity
checking policy.

**7\.**Configure the login attempt limit.

password-control login-attempt login-times \[ exceed { lock \| lock-time time \| unlock } ]

By default, the login-attempt policy of
the user group equals the global login-attempt policy.

## Setting local user password control parameters

**1\.**Enter system view.

system-view

**2\.**Create a device management or network access
user and enter its view.

¡     Create
a device management user and enter its view.

local-user user-name class manage

¡     Create
a network access user and enter its view.

local-user user-name class network

By default, no local user exists.

For information about local user
configuration, see "Configuring AAA."

**3\.**Configure the password aging time for the
local user.

password-control aging aging-time

By default, the setting equals that for
the user group to which the local user belongs. If no aging time is configured
for the user group, the global setting applies to the local user.

This command is available only for device
management users.

**4\.**Configure the minimum password length for
the local user.

password-control length length

By default, the setting equals that for
the user group to which the local user belongs. If no minimum password length
is configured for the user group, the global setting applies to the local user.

**5\.**Configure the password composition policy
for the local user.

password-control composition type-number type-number \[ type-length type-length ]

By default, the settings equal those for
the user group to which the local user belongs. If no password composition
policy is configured for the user group, the global settings apply to the local
user.

**6\.**Configure the password complexity checking
policy for the local user.

password-control complexity { same-character
\| user-name } check

By default, the settings equal those for
the user group to which the local user belongs. If no password complexity
checking policy is configured for the user group, the global settings apply to
the local user.

**7\.**Configure the login attempt limit.

password-control login-attempt login-times \[ exceed { lock \| lock-time time \| unlock } ]

By default, the settings equal those for
the user group to which the local user belongs. If no login-attempt policy is
configured for the user group, the global settings apply to the local user.

This command is available only for device
management users.

## Setting super password control parameters

**1\.**Enter system view.

system-view

**2\.**Set the password aging time for super
passwords.

password-control super aging aging-time

The default setting is 90 days.

**3\.**Configure the minimum length for super
passwords.

In non-FIPS mode:

password-control super length length

The default setting is 10 characters. 

In FIPS mode:

password-control super length length

The default setting is 15 characters.

**4\.**Configure the password composition policy
for super passwords.

In non-FIPS mode:

password-control super composition type-number type-number \[ type-length type-length ]

By default, a super password must contain
a minimum of two character types and a minimum of one character for each type.

In FIPS mode:

password-control super composition type-number type-number \[ type-length type-length ]

By default, a super password must contain
a minimum of four character types and a minimum of one character for each type.

## Display and maintenance commands for password control

Execute display commands
in any view and reset commands in user
view.

‌

| Task | Command || Display password control configuration. | display password-control \[ super ] || Display information about users in the password control blacklist. | display password-control blacklist \[ user-name user-name \| ip ipv4-address  \| ipv6 ipv6-address] || Delete users from the password control blacklist. | reset password-control blacklist \[ user-name user-name ] || Clear history password records. | reset password-control history-record \[ user-name user-name \| super \[ role role-name ] \| network-class \[ user-name user-name ] ] |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

## Password control configuration examples

### Example: Configuring password control

#### Network configuration

Configure a global password control policy
to meet the following requirements:

·     A password must contain a minimum of 16
characters.

·     A password must contain a minimum of four character
types and a minimum of four characters for each type.

·     An FTP or VTY user failing to provide the
correct password in two successive login attempts is permanently prohibited
from logging in with the current IP address.

·     A user can log in five times within 60 days
after the password expires.

·     A password expires after 30 days.

·     The minimum password update interval is 36
hours.

·     The maximum account idle time is 30 days.

·     A password cannot contain the username or the
reverse of the username.

·     A minimum of three identical consecutive characters
is not allowed in a password.

Configure a super password control policy
for user role network-operator to meet the
following requirements: 

·     A super password must contain a minimum of 24 characters.

·     A super password must contain a minimum of four character
types and a minimum of five characters for each type.

Configure a password control policy for
local Telnet user test to meet the following
requirements:

·     The password must contain a minimum of 24 characters.

·     The password must contain a minimum of four
character types and a minimum of five characters for each type.

·     The password for the local user expires after 20
days.

#### Procedure

\# Enable the password control feature globally.

\<Sysname\> system-view

\[Sysname] password-control enable

\# Allow a maximum of two consecutive login failures
on a user account, and lock the user account and the user's IP address
permanently if the limit is reached.

\[Sysname] password-control
login-attempt 2 exceed lock

\# Set all passwords to expire after 30
days.

\[Sysname] password-control aging 30

\# Globally set the minimum password length
to 16 characters.

\[Sysname] password-control length 16

\# Set the minimum password update interval
to 36 hours.

\[Sysname] password-control update-interval
36

\# Specify that a user can log in five times
within 60 days after the password expires.

\[Sysname] password-control expired-user-login
delay 60 times 5

\# Set the maximum account idle time to 30
days.

\[Sysname] password-control login
idle-time 30

\# Refuse any password that contains the
username or the reverse of the username.

\[Sysname] password-control complexity
user-name check

\# Refuse a password that contains a minimum
of three identical consecutive characters.

\[Sysname] password-control complexity
same-character check

\# Globally specify that all passwords must each
contain a minimum of four character types and a minimum of four characters for
each type.

\[Sysname] password-control
composition type-number 4 type-length 4

\# Set the minimum super password length to
24 characters.

\[Sysname] password-control super length
24

\# Specify that a super password must
contain a minimum of four character types and a minimum of five characters for
each type.

\[Sysname] password-control super
composition type-number 4 type-length 5

\# Configure a super password used for
switching to user role network-operator as 123456789ABGFTweuix@#$%! in plain text.

\[Sysname] super password role network-operator
simple 123456789ABGFTweuix@#$%!

\# Create a device management user named test.

\[Sysname] local-user test class
manage

\# Set the service type of the user to Telnet.

\[Sysname-luser-manage-test] service-type
telnet

\# Set the minimum password length to 24 for
the local user.

\[Sysname-luser-manage-test] password-control
length 24

\# Specify that the password of the local
user must contain a minimum of four character types and a minimum of five
characters for each type.

\[Sysname-luser-manage-test] password-control
composition type-number 4 type-length 5

\# Set the password for the local user to
expire after 20 days.

\[Sysname-luser-manage-test] password-control
aging 20

\# Configure the password of the local user
in interactive mode.

\[Sysname-luser-manage-test] password

Password:

Confirm :

Updating user information. Please
wait ... ...

\[Sysname-luser-manage-test] quit

#### Verifying the configuration

\# Display the global password control
configuration.

\<Sysname\> display
password-control

 Global password control
configurations:

 Password control:                     Enabled(device
management users)

                                      
Disabled (network access users)

 Password aging:                       Enabled
(30 days)

 Password length:                      Enabled
(16 characters)

 Password composition:                 Enabled
(4 types, 4 characters per type)

 Password history:                     Enabled
(max history record:4)

 Early notice on password expiration:
 7 days

 User authentication
timeout:          600 seconds

 Maximum login
attempts:               2

 Action for exceeding login attempts: 
Lock

 Minimum interval between two updates:
36 hours

 User account idle time:               30
days

 Logins with aged password:            5
times in 60 days

 Password complexity:                  Enabled
(username checking)

                                       Enabled
(repeated characters checking)

 Password change:                      Enabled
(first login)

                                      
Disabled (mandatory weak password change)

 All line:                             Disabled
(all line blacklist)

 

\# Display the password control
configuration for super passwords.

\<Sysname\> display
password-control super

 Super password control
configurations:

 Password aging:                       Enabled
(90 days)

 Password length:                      Enabled
(24 characters)

 Password composition:                 Enabled
(4 types, 5 characters per type)

\# Display the password control
configuration for local user test.

\<Sysname\> display local-user
user-name test class manage

Total 1 local users matched.

 

Device management user test:

  State:                     Active

  Service type:              Telnet

  User group:                system

  Bind attributes:

  Authorization attributes:

    Work directory:          flash:

    User role list:         
network-operator

  Password control configurations:

    Password aging:          20 days

    Password length:         24
characters

    Password composition:    4 types,
5 characters per type

    Password complexity:     username
checking

 

 

