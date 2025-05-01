
# Using Tcl

## About Tcl

Comware 7 provides a built-in tool command
language (Tcl) interpreter. From user view, you can use the tclsh command to enter Tcl configuration view to execute the following
commands:

·     Tcl 8.5 commands.

·     Comware commands.

The Tcl configuration view is equivalent to
the user view. You can use Comware commands in Tcl configuration view in the
same way they are used in user view.

## Restrictions and guidelines: Tcl

To return from a subview under Tcl
configuration view to the upper-level view, use the quit command.

To return from a subview under Tcl
configuration view to the Tcl configuration view, press Ctrl\+Z.

## Using Tcl commands to configure the device

### Restrictions and guidelines

When you use Tcl to configure the device,
follow these restrictions and guidelines:

·     You can apply Tcl environment variables to
Comware commands.

·     No online help information is provided for Tcl
commands.

·     You cannot press Tab
to complete an abbreviated Tcl command.

·     Make sure the Tcl commands can be executed
correctly. 

·     As a best practice, log in through Telnet or
SSH. You cannot stop Tcl commands by using a shortcut key or a CLI command. If
a problem occurs when the Tcl commands are being executed, you can terminate the
process by closing the connection if you logged in through Telnet or SSH. If
you logged in from the console port, you must perform one of the following
tasks:

¡     Restart
the device.

¡     Log
in to the device by using a different method, and use the free line command to release the console or AUX line. For more information
about the command, see Fundamentals Command Reference.


·     You can press Ctrl\+D
to abort Tcl command read stdin.

### Procedure

**1\.**Enter Tcl configuration view from user view.

tclsh

**2\.**Execute a Tcl command.

Tcl command

**3\.**Return from Tcl configuration view to user
view.

¡     tclquit

¡     quit

## Executing Comware commands in Tcl configuration view

### About executing Comware commands in Tcl configuration view

To execute a Comware command in Tcl
configuration view, use one of the following methods:

·     Enter the Comware command directly. If a Tcl
command uses the same command string as the Comware command, the Tcl command is
executed.

·     Prefix the Comware command with the cli keyword. If a Tcl command uses the same command string as the Comware
command, the Comware command is executed.

### Restrictions and guidelines

Follow these restrictions and guidelines
when you execute Comware commands in Tcl configuration view:

·     To specify a string enclosed in quotation marks
(") or braces ({ and }), you must use the escape character (\\) before the quotation
marks or braces. For example, to specify "a" as
the description for an interface, you must enter description
\\"a\\". If you enter description
"a", the description is a.

·     For Comware commands, you can enter ? to obtain online help or press Tab to
complete an abbreviated command. For more information, see "Using the
CLI."

·     The cli command
is a Tcl command, so you cannot enter ? to obtain
online help or press Tab to complete an abbreviated
command.

·     Successfully executed Comware commands are saved
to command history buffers. You can use the upper arrow or lower arrow key to obtain
executed commands.

·     To execute multiple Comware commands in one
operation, use one of the following methods:

¡     Enter
multiple Comware commands separated by semi-colons to execute the commands in
the order they are entered. For example, vlan 2;description Tech.

¡     Specify
multiple Comware commands for the cli command,
quote them, and separate them by a space and a semicolon. For example, cli "vlan
2 ;description Tech".

¡     Specify
one Comware command for each cli command
and separate them by a space and a semicolon. For example, cli vlan 2 ;cli
description Tech.

### Procedure

**1\.**Enter Tcl configuration view

tclsh

**2\.**Execute Comware commands.

¡     Execute
Comware commands directly.

Command

¡     Execute
Comware commands by using the cli command.

cli command

**3\.**Return from Tcl configuration view to user
view.

¡     tclquit

¡     quit

 

