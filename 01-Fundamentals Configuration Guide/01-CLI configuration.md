
# Using the CLI

## About the CLI

At the command-line
interface (CLI), you can enter text commands to configure, manage, and monitor the
device. 

You can use different methods to log in to
the CLI. For example, you can log in through the console port or Telnet. For
more information about login methods, see "Login overview."

## Using CLI views

### About CLI views

Commands are grouped in different views by feature.
To use a command, you must enter its view.

CLI views are hierarchically organized, as
shown in [Figure 1](#_Ref264645249).
Each view has a unique prompt, from which you can identify where you are and
what you can do. For example, the prompt \[Sysname-vlan100] shows that you are in VLAN 100 view and can configure attributes
for that VLAN.

Figure 1 CLI views

![](https://resource.h3c.com/en/202407/12/20240712_11704065_x_Img_x_png_0_2215953_294551_0.png)

‌

You are placed in user view immediately after you log in to the CLI.

In user view, you can perform the following
tasks:

·     Perform basic operations including display,
debug, file management, FTP, Telnet, clock setting, and reboot.

·     Enter system view.

In system view, you can perform the
following tasks:

·     Configure settings that affect the device as a
whole, such as the daylight saving time, banners, and hotkeys. 

·     Enter feature views. 

For example, you can perform the
following tasks:

¡     Enter
interface view to configure interface parameters.

¡     Enter
VLAN view to add ports to the VLAN.

¡     Enter
user line view to configure login user attributes. 

A feature view might have child views.
For example, NQA operation view has the child view HTTP operation view.

·     Enter probe view by using the probe command.

The probe view provides display,
debugging, and maintenance commands, which are mainly used by developers and
testers for system fault diagnosis and system operation monitoring.

 

| CAUTION | CAUTION:  Use the commands in probe view under the guidance of engineers to avoid system exceptions caused by incorrect operations. |
| --- | --- |

‌

For more information about the commands
in probe view, see the probe commands manual for each feature.

To display all commands available in a
view, enter a question mark (?) at the view prompt.

### Entering system view from user view

To enter system view from user view,
execute the following command:

system-view

### Returning to the upper-level view from a view

#### Restrictions and guidelines

Executing the quit command in user view terminates your connection to the device.

To return from public key view to system
view, you must use the peer-public-key end
command.

#### Procedure

To return to the upper-level view from a
view, execute the following command:

quit

### Returning to user view

#### About this task

This feature enables you to return to user
view from any other view by performing a single operation. You do not need to
execute the quit command multiple
times.

#### Procedure

To return directly to user view from any
other view except Tcl configuration view and the Python shell, use one of the
following methods:

·     Execute the return command.

·     Press Ctrl\+Z.

To return to user view from Tcl
configuration view, use the tclquit
command. 

To return to user view from the Python
shell, use the exit() command.

## Accessing the CLI online help

The CLI online help is context sensitive. Enter
a question mark at any prompt or in any position of a command to display all
available options.

To access the CLI online help, use one of
the following methods:

·     Enter a question mark at a view prompt to
display the first keyword of every command available in the view. For example:

\<Sysname\> ?

User view commands:

  archive             Archive
configuration                                     

  arp                 Address
Resolution Protocol (ARP) module                  

  backup              Backup
the startup configuration file to a TFTP server    

  boot-loader         Software
image file management

... 

·     Enter a space and a question mark after a
command keyword to display all available keywords and arguments. 

¡     If
the question mark is in the place of a keyword, the CLI displays all possible
keywords, each with a brief description. For example:

\<Sysname\> terminal ?

  debugging  Enable to display
debugging logs on the current terminal

  logging    Display logs on the
current terminal

  monitor    Enable to display
logs on the current terminal

¡     If
the question mark is in the place of an argument, the CLI displays the
description for the argument. For example:

\<Sysname\> system-view

\[Sysname] interface vlan-interface
?

  \<1-4094\> 
Vlan-interface interface number

\[Sysname] interface
vlan-interface 1 ?

  \<cr\>

\[Sysname] interface
vlan-interface 1

\<1-4094\> is the value range for the argument. \<cr\> indicates that the command
is complete and you can press Enter to execute the command.

·     Enter an incomplete keyword string followed by a
question mark to display all keywords starting with that string. The CLI also displays
the descriptions for the keywords. For example:

\<Sysname\> f?

  fdisk    Partition a storage
medium

  fixdisk   Check and repair a
storage medium

  format    Format a storage
medium

  free      Release a connection

  ftp       Open an FTP
connection

\<Sysname\> display ftp?

  ftp         FTP module

  ftp-server  FTP server
information

  ftp-user    FTP user
information

## Using the undo form of a command

Most configuration commands have an undo form for the following tasks:

·     Canceling a configuration.

·     Restoring the default.

·     Disabling a feature. 

For example, the info-center
enable command enables the information center. The
undo
info-center enable command disables the
information center.

## Entering a command

When you enter a
command, you can perform the following tasks:

·     Use keys or hotkeys to edit the command line.

·     Use abbreviated keywords or keyword aliases.

### Editing a command line

To edit a command line, use the keys listed
in [Table 1](#_Ref301795249) or
the hotkeys listed in [Table 4](#_Ref465686031). When
you are finished, you can press Enter to execute the
command.

The command edit buffer can contain a
maximum of 511 characters. If the total length of a command line exceeds the
limit after you press Tab to complete the last
keyword or argument, the system does not complete the keyword.

Table 1 Command line editing keys

| Keys | Function |
| --- | --- |
| Common keys | If the edit buffer is not full, pressing a common key inserts a character at the cursor and moves the cursor to the right. The edit buffer can store up to 511 characters. Unless the buffer is full, all common characters that you enter before pressing Enter are saved in the edit buffer. || Backspace | Deletes the character to the left of the cursor and moves the cursor back one character. || Left arrow key (←) | Moves the cursor one character to the left. || Right arrow key (→) | Moves the cursor one character to the right. || Up arrow key (↑) | Displays the previous command in the command history buffer. || Down arrow key (↓) | Displays the next command in the command history buffer. || Tab | If you press Tab after typing part of a keyword, the system automatically completes the keyword.  ·     If a unique match is found, the system displays the complete keyword.  ·     If there is more than one match, press Tab multiple times to pick the keyword you want to enter.  ·     If there is no match, the system does not modify what you entered but displays it again in the next line. |







‌

The device supports the following special
commands:

·     \#–Used by the system in a configuration
file as separators for adjacent sections.

·     version–Used by the system in a
configuration file to indicate the software version information. For example, version 7.1.
xxx, Release xxx.

These commands are special because of the
following reasons:

·     These commands are not intended for you to use
at the CLI.

·     You can enter the \# command in any view or the version
command in system view, or enter any values for them. For example, you can
enter \# abc or version abc. However, the settings do not take effect.

·     The device does not provide any online help
information for these commands.

### Entering a text or string type value for an argument

A text type argument value can contain any characters
except question marks (?).

A string type argument value can contain any
printable characters except question marks (?).

·     To include a quotation mark (") or backward
slash (\\) in a string type argument value, prefix the character with an escape
key (\\), for example, \\" and \\\\.

·     To include a blank space in a string type
argument value, enclose the value in quotation marks, for example, ''my device''.

A specific argument might have more
requirements. For more information, see the relevant command reference.

To enter a printable character, you can enter
the character or its ASCII code in the range of 32 to 126\.

### Entering an interface type

You can enter an interface type in one of
the following formats:

·     Full spelling of the interface type.

·     An abbreviation that uniquely identifies the
interface type.

·     Acronym of the interface type. 

For a command line, all interface types are
case insensitive. The following table shows the full spellings and acronyms of
interface types. 

For example, to use the interface command to enter the view of interface Ten-GigabitEthernet 1/0/1, you can enter the
command line in the following formats:

·     interface ten-gigabitethernet
1/0/1

·     interface ten 1/0/1

·     interface xge 1/0/1

Spaces between the interface types and
interfaces are not required.

Table 2 Full spellings and acronyms of interface types

| Full spelling | Acronym |
| --- | --- |
| Bridge-Aggregation | BAGG || FortyGigE | FGE || GigabitEthernet | GE || InLoopBack | InLoop || LoopBack | Loop || M-GigabitEthernet | MGE || Multicast Tunnel | MTunnel || NULL | NULL || Register-Tunnel | REG || Route-Aggregation | RAGG || Ten-GigabitEthernet | XGE || Tunnel | Tun || TwentyGigE | TGE || Vsi-interface | Vsi || Vlan-interface | Vlan-int |















‌

### Abbreviating commands

You can enter a command line quickly by
entering incomplete keywords that uniquely identify the complete command. In
user view, for example, commands starting with an s
include startup saved-configuration and system-view. To enter
the command system-view, you need
to type only sy. To enter the command
startup saved-configuration, type
st
s.

You can also press Tab
to complete an incomplete keyword. 

### Configuring and using command aliases

#### About this task

You can configure one or more aliases for a
command or the starting keywords of commands. Then, you can use the aliases to
execute the command or commands. If the command or commands have undo forms, you can also use the aliases to execute the undo command or commands.

For example, if you configure the shiprt alias for display ip routing-table, you can enter shiprt to execute the display ip
routing-table command. If you configure the ship alias for display ip, you can use
ship to execute all commands starting with display ip, including:

·     Enter ship routing-table to execute the display ip routing-table command.

·     Enter ship interface to execute the display ip interface
command.

The device provides a set of system-defined
command aliases, as listed in [Table 3](#_Ref454788780).

Table 3 System-defined command aliases

| Command alias | Command or command keyword |
| --- | --- |
| access-list | acl || end | return || erase | delete || exit | quit || hostname | sysname || logging | info-center || no | undo || show | display || write | save |









‌

#### Restrictions and guidelines

A command alias can be used only as the
first keyword of a command or the second keyword of the undo form of a command.

After you successfully execute a command by
using an alias, the system saves the command, instead of the alias, to the running
configuration. 

The command string can include up to nine
parameters. Each parameter starts with the dollar sign ($) and a sequence
number in the range of 1 to 9\. For example, you can configure the alias shinc for the display ip $1 \| include $2
command. Then, to execute the display ip routing-table \| include Static command, you need to enter only shinc routing-table Static.

To use an alias for a command that has parameters,
you must specify a value for each parameter. If you fail to do so, the system informs
you that the command is incomplete and displays the command string represented
by the alias.

System-defined command aliases cannot be
deleted.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a command alias.

alias alias command

By default, the device has a set of
command aliases, as listed in [Table 3](#_Ref454788780).

**3\.**(Optional.) Display command aliases.

display alias \[ alias ]

This command is available in any view.

### Configuring and using hotkeys

#### About this task

The device supports a set of hotkeys.
Pressing a hotkey executes the command or function assigned to the hotkey. [Table 4](#_Ref465686031) shows
the hotkeys and their default definitions. You can configure all the hotkeys
except Ctrl\+].

If a hotkey is also defined by the terminal
software you are using to interact with the device, you can reconfigure the
hotkey or remove the hotkey. 

#### Restrictions and guidelines

A hotkey can correspond to only one command
or function. If you assign multiple commands or functions to the same hotkey,
the most recently assigned command or function takes effect. 

A command or function can be assigned to
multiple hotkeys. You can use any of the hotkeys to execute the command or
function.

If a hotkey is also defined by the terminal
software you are using to interact with the device, the terminal software
definition takes effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Assign a command to a hotkey.

hotkey hotkey { command \| function function \| none }

[Table 4](#_Ref465686031) shows
the default definitions for the hotkeys.

**3\.**(Optional.) Display hotkeys.

display hotkey

This command is available in any view.

Table 4 Default
definitions for hotkeys

| Hotkey | Function or command |
| --- | --- |
| Ctrl\+A | move\_the\_cursor\_to\_the\_beginning\_of\_the\_line: Moves the cursor to the beginning of a line. || Ctrl\+B | move\_the\_cursor\_one\_character\_to\_the\_left: Moves the cursor one character to the left. || Ctrl\+C | stop\_the\_current\_command: Stops the current command. || Ctrl\+D | erase\_the\_character\_at\_the\_cursor: Deletes the character at the cursor. || Ctrl\+E | move\_the\_cursor\_to\_the\_end\_of\_the\_line: Moves the cursor to the end of a line. || Ctrl\+F | move\_the\_cursor\_one\_character\_to\_the\_right: Moves the cursor one character to the right. || Ctrl\+G | display current-configuration: Display the running configuration. || Ctrl\+H | erase\_the\_character\_to\_the\_left\_of\_the\_cursor: Deletes the character to the left of the cursor. || Ctrl\+L | display ip routing-table: Display the IPv4 routing table information. || Ctrl\+N | display\_the\_next\_command\_in\_the\_history\_buffer: Displays the next command in the history buffer. Password configuration commands, if any, are skipped. || Ctrl\+O | undo debugging all: Disable all debugging functions. || Ctrl\+P | display\_the\_previous\_command\_in\_the\_history\_buffer: Displays the previous command in the history buffer. Password configuration commands, if any, are skipped. || Ctrl\+R | redisplay\_the\_current\_line: Redisplays the current line. || Ctrl\+T | N/A || Ctrl\+U | N/A || Ctrl\+W | delete\_the\_word\_to\_the\_left\_of\_the\_cursor: Deletes the word to the left of the cursor. || Ctrl\+X | delete\_all\_characters\_from\_the\_beginning\_of\_the\_line\_to\_the\_cursor: Deletes all characters to the left of the cursor. || Ctrl\+Y | delete\_all\_characters\_from\_the\_cursor\_to\_the\_end\_of\_the\_line: Deletes all characters from the cursor to the end of the line. || Ctrl\+Z | return\_to\_the\_User\_View: Returns to user view. || Ctrl\+] | kill\_incoming\_connection\_or\_redirect\_connection: Terminates the current connection. || Esc\+B | move\_the\_cursor\_back\_one\_word: Moves the cursor back one word. || Esc\+D | delete\_all\_characters\_from\_the\_cursor\_to\_the\_end\_of\_the\_word: Deletes all characters from the cursor to the end of the word. || Esc\+F | move\_the\_cursor\_forward\_one\_word: Moves the cursor forward one word. |























‌

### Enabling redisplaying entered-but-not-submitted commands

#### About this task

Your input might be interrupted by system
information output. If redisplaying entered-but-not-submitted commands is
enabled, the system redisplays your input after finishing the output. You can then
continue entering the command line.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable redisplaying
entered-but-not-submitted commands.

info-center synchronous

By default, the system does not redisplay
entered-but-not-submitted commands.

For more information about this command,
see Network Management and Monitoring Command Reference.

## Understanding command-line syntax error messages

After you press Enter
to submit a command, the command line interpreter examines the command syntax.

·     If the command passes syntax check, the CLI
executes the command. 

·     If the command fails syntax check, the CLI displays
an error message.

Table 5 Common
command-line syntax error messages

| Syntax error message | Cause |
| --- | --- |
| % Unrecognized command found at '^' position. | The keyword in the marked position is invalid. || % Incomplete command found at '^' position. | One or more required keywords or arguments are missing. || % Ambiguous command found at '^' position. | The entered character sequence matches more than one command. || % Too many parameters found at '^' position. | The entered character sequence contains excessive keywords or arguments. || % Wrong parameter found at '^' position. | The argument in the marked position is invalid. |





‌

## Using the command history feature

### About command history buffers

The system automatically saves commands successfully
executed by a login user to the following two command history buffers: 

·     Command history buffer for the user line.

·     Command history buffer for all user lines. 

Table 6 Comparison between the two types of
command history buffers

| Item | Command history buffer for a user line | Command history buffer for all user lines |
| --- | --- | --- |
| Which commands are saved in the buffer? | Commands successfully executed by the current user of the user line. | Commands successfully executed by all login users. || Can commands in the buffer be displayed? | Yes. | Yes. || Can commands in the buffer be recalled? | Yes. | No. || Are buffered commands cleared when the user logs out? | Yes. | No. || Is the buffer size adjustable? | Yes. | No. The buffer size is fixed at 1024\. |





‌

### Command buffering rules

The system follows these rules when buffering
commands:

·     If you use incomplete keywords when entering a
command, the system buffers the command in the exact form that you used. 

·     If you use an alias when entering a command, the
system transforms the alias to the represented command or command keywords before
buffering the command.

·     If you enter a command in the same format
multiple times in succession, the system buffers the command only once. If you
enter a command in different formats multiple times, the system buffers each
command format. For example, display cu
and display
current-configuration are buffered as two
entries but successive repetitions of display cu
create only one entry.

·     To buffer a new command when a buffer is full,
the system deletes the oldest command entry in the buffer.

### Managing and using the command history buffers

#### Displaying the commands in command history buffers

To display the commands in command history
buffers, execute the following commands in any view:

·     Display the commands in command history buffers
for a user line.

display history-command

·     Display the commands in command history buffers
for all user lines.

display history-command all

#### Recalling commands in the command history buffer for a user line

| IMPORTANT | IMPORTANT:  Password configuration commands cannot be recalled. |
| --- | --- |

 

Use up and down arrow keys to navigate to a
command and press Enter.

#### Setting the size of the command history buffer for a user line

Use the history-command max-size command in
user line or user line class view. For more information, see Fundamentals Command Reference.

### Repeating commands in the command history buffer for a user line

#### About this task

You can recall and execute commands in the
command history buffer for the current user line multiple times. 

#### Restrictions and guidelines

The repeat command
is available in any view. However, to repeat a command, you must first enter
the view for the command. To repeat multiple commands, you must first enter the
view for the first command.

The repeat command
executes commands in the order they were executed.

The system starts a timer and waits for
your interaction when it repeats an interactive command. If you do not provide
the required information at prompt before the timer expires, the system skips
the interactive command.

The system skips all password configuration
commands.

#### Procedure

To repeat commands in the command history
buffer for the current user line, execute the following command:

repeat \[ number ] \[ count times ] \[ delay seconds ]

## Controlling the CLIoutput

This section describes the CLI output
control features that help you identify the desired output. 

### Pausing between screens of output

#### About this task

The device can automatically pause after
displaying a specific number of lines if the output is too long to fit on one
screen. At a pause, the device displays ----more----.
You can use the keys described in [Table 7](#_Ref318360207) to
display more information or stop the display. 

You can also disable pausing between
screens of output for the current session. Then, all output is displayed at one
time and the screen is refreshed continuously until the final screen is
displayed.

Table 7 Output controllingkeys

| Keys | Function |
| --- | --- |
| Space | Displays the next screen. || Enter | Displays the next line. || Ctrl\+C | Stops the display and cancels the command execution. || \<PageUp\> | Displays the previous page. || \<PageDown\> | Displays the next page. |





‌

#### Disabling pausing between screens of output

To disable pausing between screens of
output, execute the following command in user view:

screen-length disable

The default depends on the settings of the screen-length command in user line view. The following are the default settings
for the screen-length command:

·     Pausing between screens of output is enabled.

·     The maximum number of lines to be displayed at a
time is 24\. 

For more information about the screen-length command, see Fundamentals Command Reference.

This command is a one-time command and
takes effect only for the current CLI session.

### Numbering each output line from a display command

#### About this task

For easy identification, you can use the \| by-linenum option to display a number for each output line from a display command. 

Each line number is displayed as a
5-character string and might be followed by a colon (:) or hyphen (-). If you
specify both \| by-linenum and \| begin regular-expression for
a display command, a hyphen is displayed for all lines that do not match the
regular expression.

#### Procedure

To number each output line from a display command, execute the following command in any view:

display command \| by-linenum

#### Example

\# Display information about VLAN 999,
numbering each output line.

\<Sysname\> display vlan 999 \|
by-linenum

    1:  VLAN ID: 999

    2:  VLAN type: Static

    3:  Route interface: Configured

    4:  IPv4 address: 192.168.2.1

    5:  IPv4 subnet mask:
255.255.255.0

    6:  Description: For LAN Access

    7:  Name: VLAN 0999

    8:  Tagged ports:   None

    9:  Untagged ports: None

### Filtering the output from a display command

#### About this task

You can use the \[ \| \[ by-linenum ] { begin \| exclude \| include } regular-expression ]\&\<1-128\> option to filter the output from a display command.

·     You can use the option to specify a
maximum of 128 filter conditions. The system displays only output lines that
meet all the conditions.

·     by-linenum—Displays a number before each
output line. You need to specify this keyword in only one filter condition.

·     begin—Displays the first line matching
the specified regular expression and all subsequent lines.

·     exclude—Displays all lines not matching
the specified regular expression. 

·     include—Displays all lines matching the
specified regular expression.

·     regular-expression—A case-sensitive
string of 1 to 256 characters, which can contain the special characters described
in [Table 8](#_Ref317614264).

Table 8 Special characters supported in a
regular expression

| Characters | Meaning | Examples |
| --- | --- | --- |
| ^ | Matches the beginning of a line. | "^u" matches all lines beginning with "u". A line beginning with "Au" is not matched. || $ | Matches the end of a line. | "u$" matches all lines ending with "u". A line ending with "uA" is not matched. || . (period) | Matches any single character. | ".s" matches "as" and "bs". || \* | Matches the preceding character or string zero, one, or multiple times. | "zo\*" matches "z" and "zoo", and "(zo)\*" matches "zo" and "zozo". || \+ | Matches the preceding character or string one or multiple times. | "zo\+" matches "zo" and "zoo", but not "z". || \| | Matches the preceding or succeeding string. | "def\|int" matches a line containing "def" or "int". || ( ) | Matches the string in the parentheses, usually used together with the plus sign (\+) or asterisk sign (\*). | "(123A)" matches "123A".  "408(12)\+" matches "40812" and "408121212", but not "408". || \\N | Matches the preceding strings in parentheses, with the Nth string repeated once. | "(string)\\1" matches a string containing "stringstring".  "(string1)(string2)\\2" matches a string containing "string1string2string2".  "(string1)(string2)\\1\\2" matches a string containing " string1string2string1string2". || \[ ] | Matches a single character in the brackets. | "\[16A]" matches a string containing 1, 6, or A; "\[1-36A]" matches a string containing 1, 2, 3, 6, or A (- is a hyphen).  To match the character "]", put it immediately after "\[", for example, \[]abc]. There is no such limit on "\[". || \[^] | Matches a single character that is not in the brackets. | "\[^16A]" matches a string that contains one or more characters except for 1, 6, or A, such as "abc". A match can also contain 1, 6, or A (such as "m16"), but it cannot contain these three characters only (such as 1, 16, or 16A). || {n} | Matches the preceding character n times. The number n must be a nonnegative integer. | "o{2}" matches "food", but not "Bob". || {n,} | Matches the preceding character n times or more. The number n must be a nonnegative integer. | "o{2,}" matches "foooood", but not "Bob". || {n,m} | Matches the preceding character n to m times or more. The numbers n and m must be nonnegative integers and n cannot be greater than m. | " o{1,3}" matches "fod", "food", and "foooood", but not "fd". || \\\< | Matches a string that starts with the pattern following \\\<. A string that contains the pattern is also a match if the characters preceding the pattern are not digits, letters, or underscores. | "\\\<do" matches "domain" and "doa". || \\\> | Matches a string that ends with the pattern preceding \\\>. A string that contains the pattern is also a match if the characters following the pattern are not digits, letters, or underscores. | "do\\\>" matches "undo" and "cdo". || \\b | Matches a word that starts with the pattern following \\b or ends with the pattern preceding \\b. | "er\\b" matches "never", but not "verb" or "erase".  "\\ber" matches "erase", but not "verb" or "never". || \\B | Matches a word that contains the pattern but does not start or end with the pattern. | "er\\B" matches "verb", but not "never" or "erase". || \\w | Same as \[A-Za-z0-9\_], matches a digit, letter, or underscore. | "v\\w" matches "vlan" and "service". || \\W | Same as \[^A-Za-z0-9\_], matches a character that is not a digit, letter, or underscore. | "\\Wa" matches "-a", but not "2a" or "ba". || \\ | Escape character. If a special character listed in this table follows \\, the specific meaning of the character is removed. | "\\\\" matches a string containing "\\", "\\^" matches a string containing "^", and "\\\\b" matches a string containing "\\b". |




















‌

#### Restrictions and guidelines

The required filtering time increases with
the complexity of the regular expression. To abort the filtering process, press
Ctrl\+C.

#### Examples

\# Display the running configuration,
starting from the first configuration line that contains line.
(The output depends on the device model and the current configuration.)

\<Sysname\> display
current-configuration \| begin line

line class
aux                                                                  

 user-role
network-admin                                                        

\#                                                                              


line class
vty                                                                  

 user-role
network-operator                                                     

\#                                                                              


line aux
0                                                                      

 user-role network-admin                                                       


\#                                                                              


line vty 0
63                                                                   

 authentication-mode none                                                       

 user-role
network-admin                                                        

 user-role
network-operator                                                     

\#

... 

\# Display brief information about interfaces
in up state.

\<Sysname\> display interface
brief \| exclude DOWN

Brief information on interfaces in
route mode:

Link: ADM \- administratively down;
Stby \- standby

Protocol: (s) \- spoofing

Interface            Link Protocol
Primary IP      Description

InLoop0              UP   UP(s)    \--

NULL0                UP   UP(s)    \--

Vlan1                UP   UP      
192.168.1.83

 

Brief information on interfaces in
bridge mode:

Link: ADM \- administratively down;
Stby \- standby

Speed: (a) \- auto

Duplex: (a)/A \- auto; H \- half; F \-
full

Type: A \- access; T \- trunk; H \-
hybrid

Interface            Link Speed   
Duplex Type PVID Description

XGE1/0/1              UP   10G(a) F(a)  A   
1

\# Display SNMP-related running
configuration lines.

\<Sysname\> display
current-configuration \| include snmp

snmp-agent

 snmp-agent community write private

 snmp-agent community read public

 snmp-agent sys-info version all

 snmp-agent target-host trap address
udp-domain 192.168.1.26 params securityname public

\# Display log entries in the log buffer that
contain both SHELL and VTY.

\<Sysname\> display logbuffer \|
include SHELL \| include VTY

%Sep  6 10:38:12:320 2018 Sysname SHELL/5/SHELL\_LOGIN:
VTY logged in from 169.254.100.171.

%Sep  6 10:52:32:576 2018 Sysname SHELL/5/SHELL\_LOGOUT:
VTY logged out from 169.254.100.171.

%Sep  6 16:03:27:100 2018 Sysname SHELL/5/SHELL\_LOGIN:
VTY logged in from 169.254.100.171.

%Sep  6 16:44:18:113 2018 Sysname SHELL/5/SHELL\_LOGOUT:
VTY logged out from 169.254.100.171.

### Saving the output from a display command to a file

#### About this task

A display
command shows certain configuration and operation information of the device.
Its output might vary over time or with user configuration or operation. You
can save the output to a file for future retrieval or troubleshooting.

Use one of the following methods to save the
output from a display command: 

·     Save the output to a separate file. Use this
method if you want to use one file for a single display command. 

·     Append the output to the end of a file. Use this
method if you want to use one file for multiple display commands. 

#### Procedure

To save the output from a display command to a file, use one of the following commands in any view:

·     Save the output from a display command to a separate file.

display command \> filename

·     Append the output from a display command to the end of a file.

display command \>\> filename

#### Examples

\# Save the VLAN 1 settings to a separate
file named vlan.txt.

\<Sysname\> display vlan 1 \> vlan.txt

\# Verify that the VLAN 1 settings are saved
to the file vlan.txt.

\<Sysname\> more vlan.txt

VLAN ID: 1

 VLAN type: Static

 Route interface: Not configured

 Description: VLAN 0001

 Name: VLAN 0001

 Tagged ports:   None

 Untagged ports: None

\# Append the VLAN 999 settings to the end
of the file vlan.txt.

\<Sysname\> display vlan 999 \>\>
vlan.txt

\# Verify that the VLAN 999 settings are
appended to the end of the file vlan.txt.

\<Sysname\> more vlan.txt

VLAN ID: 1

 VLAN type: Static

 Route interface: Not configured

 Description: VLAN 0001

 Name: VLAN 0001

 Tagged ports:   None

 Untagged ports: None

 VLAN ID: 999

 VLAN type: Static

 Route interface: Configured

 IP address: 192.168.2.1

 Subnet mask: 255.255.255.0

 Description: For LAN Access

 Name: VLAN 0999

 Tagged ports:   None

 Untagged ports: None

### Viewing and managing the output from a display command effectively

You can use the following methods in
combination to filter and manage the output from a display command:

·     [Numbering each output line from a
display command](#_Ref301949167)

·     [Filtering the output from a display command](#_Ref353268617)

·     [Saving the output from a display command
to a file](#_Ref298332868)

#### Procedure

To use multiple measures to view and manage
the output from a display command
effectively, execute the following command in any view:

display command \[ \| \[ by-linenum ] { begin \| exclude \| include } regular-expression ]\&\<1-128\> \[ \> filename \| \>\> filename ]

#### Examples

\# Save the running configuration to a separate
file named test.txt, with each line numbered.

\<Sysname\> display
current-configuration \| by-linenum \> test.txt

\# Append lines including snmp in the running configuration to the file test.txt.

\<Sysname\> display
current-configuration \| include snmp \>\> test.txt

\# Display the first line that begins with user-group
in the running configuration and all the following lines.

\<Sysname\> display
current-configuration \| by-linenum begin user-group

  114:  user-group system

  115-  \#

  116-  return

// The colon (:) following a line number
indicates that the line contains the string user-group. The hyphen (-)
following a line number indicates that the line does not contain the string user-group.

 

