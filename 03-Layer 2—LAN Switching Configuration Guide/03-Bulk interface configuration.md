
# Bulk configuring interfaces

## About interface bulk configuration

You can enter
interface range view to bulk configure multiple interfaces with the same
feature instead of configuring them one by one. For example, you can execute
theshutdown command in interface range view
to shut down a range of interfaces.

To configure interfaces in bulk, you must
configure an interface range and enter its view by using the interface range or interface range name
command.

The interface range created by using the interface range command is not saved to the running configuration. You cannot use the
interface range repeatedly. To create an interface range that can be used
repeatedly, use the interface range name command.

## Restrictions and guidelines: Bulk interface configuration

When you bulk configure interfaces in
interface range view, follow these restrictions and guidelines:

·     In interface range view, only the commands
supported by the first interface in the specified interface list (alphabetically
sorted) are available for configuration. 

·     Before you configure an interface as the first
interface in an interface range, make sure you can enter the view of the
interface by using the interface interface-type { interface-number \| interface-number.subnumber } command. 

·     Do not assign both an aggregate interface and
any of its member interfaces to an interface range. Some commands, after being
executed on both an aggregate interface and its member interfaces, can break up
the aggregation. 

·     Understand that the more interfaces you specify,
the longer the command execution time. 

·     To guarantee bulk interface configuration performance,
configure fewer than 1000 interface range names.

The device does not output prompt or alarm
messages during the bulk interface configuration process. Make sure you are
fully aware of the support and impacts of bulk interface configuration. 

·     After a command is executed in interface range
view, one of the following situations might occur:

¡     The
system displays an error message and stays in interface range view. This means
that the execution failed on one or multiple member interfaces. 

\-     If the execution failed on the first member interface, the command
is not executed on any member interfaces. 

\-     If the execution failed on a non-first member interface, the command
takes effect on the remaining member interfaces.

¡     The
system returns to system view. This means that: 

\-     The command is supported in both system view and interface view. 

\-     The execution failed on a member interface in interface range view
and succeeded in system view. 

\-     The command is not executed on the subsequent member interfaces. 

You can use the display this command to verify the configuration in interface view of each
member interface. In addition, if the configuration in system view is not
needed, use the undo form of the
command to remove the configuration.

## Procedure

**1\.**Enter system view.

system-view

**2\.**Create an interface range and enter
interface range view.

¡     Create an interface range without specifying a name.

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

¡     Create a named interface range.

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

**3\.**(Optional.) Display commands available for
the first interface in the interface range.

Enter a question mark (?) at the
interface range prompt.

**4\.**Use available commands to configure the
interfaces.

Available commands depend on the
interface.

**5\.**(Optional.) Verify the configuration.

display this

## Display and maintenance commands for bulk interface configuration

Execute the display command in any view. 

 

| Task | Command |
| --- | --- |
| Display information about the interface ranges created by using the interface range name command. | display interface range \[ name name ] |

‌

