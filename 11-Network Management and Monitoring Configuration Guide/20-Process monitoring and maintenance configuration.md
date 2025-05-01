
# Monitoring and maintaining processes

## About monitoring and maintaining processes

The system software of the device is a full-featured,
modular, and scalable network operating system based on the Linux kernel. The
system software features run the following types of independent processes:

·     User process—Runs in user space. Most system software features run user processes.
Each process runs in an independent space so the failure of a process does not
affect other processes. The system automatically monitors user processes. The
system supports preemptive multithreading. A process can run multiple threads to
support multiple activities. Whether a process supports multithreading depends
on the software implementation.

·     Kernel thread—Runs in kernel space. A kernel thread executes kernel code. It has a
higher security level than a user process. If a kernel thread fails, the system
breaks down. You can monitor the running status of kernel threads.

## Process monitoring and maintenance tasks at a glance

To monitor and maintain processes, perform
the following tasks:

·     Monitoring and maintaining user processes

¡     [Monitoring and maintaining processes](#_Ref472597506)

The commands in
this section apply to both user processes and kernel threads.

¡     [Monitoring and maintaining user processes](#_Ref327265181)

The commands in this section apply only
to user processes.

·     Monitoring and maintaining kernel threads

¡     [Monitoring and maintaining processes](#_Ref472597506)

The commands in
this section apply to both user processes and kernel threads.

¡     [Monitoring and maintaining kernel threads](#_Ref327265187)

The commands in
this section apply only to kernel threads.

## Starting or stopping a third-party process

### About third-party processes

Third-party processes do not start up
automatically. Use this feature to start or stop a third-party process, such as
Puppet or Chef.

### Starting a third-party process

#### Restrictions and guidelines

If you execute the third-part-process
start command multiple times but specify the
same process name, whether the command can be executed successfully depends on
the process. You can use the display current-configuration \| include
third-part-process command to view the result.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Start a third-party process.

third-part-process start name process-name \[ arg args ]

### Stopping a third-party process

**1\.**Display the IDs of third party processes.

display process all 

This command is available in any view. "Y"
in the THIRD field from the output indicates a third-party process, and the PID
field indicates the ID of the process. 

**2\.**Enter system view.

system-view 

**3\.**Stop a third-party process.

third-part-process stop pid pid\&\<1-10\>

This command can be used to stop only processes
started by the third-part-process start command.

## Monitoring and maintaining processes

#### About this task

The commands in this section apply to both user
processes and kernel threads. You can use the commands for the following
purposes:

·     Display the overall memory usage.

·     Display the running processes and their memory
and CPU usage.

·     Locate abnormal processes.

If a process consumes excessive memory or
CPU resources, the system identifies the process as an abnormal process.

·     If an abnormal process is a user process,
troubleshoot the process as described in "[Monitoring and maintaining user
processes](#_Ref327265181)."

·     If an abnormal process is a kernel thread,
troubleshoot the process as described in "[Monitoring and maintaining kernel
threads](#_Ref327265187)."

#### Procedure

Execute the following commands in any view.

 

| Task | Command |
| --- | --- |
| Display memory usage. | display memory \[ summary ] \[ slot slot-number \[ cpu cpu-number ] ] || Display process state information. | display process \[ all \| job job-id \| name process-name ] \[ slot slot-number \[ cpu cpu-number ] ] || Display CPU usage for all processes. | display process cpu \[ slot slot-number \[ cpu cpu-number ] ] || Monitor process running state. | monitor process \[ dumbtty ] \[ iteration number ] \[ slot slot-number \[ cpu cpu-number ] ] || Monitor thread running state. | monitor thread \[ dumbtty ] \[ iteration number ] \[ slot slot-number \[ cpu cpu-number ] ] |





‌

For more information about the display memory command, see Fundamentals Command Reference.

## Monitoring and maintaininguser processes

### About monitoring and maintaining user processes

Use this feature to monitor abnormal user
processes and locate problems.

### Configuring core dump

#### About this task

The core dump feature enables the system to
generate a core dump file each time a process crashes until the maximum number
of core dump files is reached. A core dump file stores information about the
process. You can send the core dump files to H3C Support to troubleshoot the
problems.

#### Restrictions and guidelines

Core dump files consume storage resources. Enable
core dump only for processes that might have problems.

#### Procedure

Execute the following commands in user view:

**1\.**(Optional.) Specify the directory for saving
core dump files.

exception filepath directory

By default, the directory for saving core
dump files is the root directory of the default file system. For more
information about the default file system, see file system management in Fundamentals Configuration Guide.

**2\.**Enable core dump for a process and specify
the maximum number of core dump files, or disable core dump for a process.

process core { maxcore value \| off } { job job-id \| name process-name }

By default, a process generates a core
dump file for the first exception and does not generate any core dump files for
subsequent exceptions.

### Display and maintenance commandsfor user processes

Execute display
commands in any view and other commands in user view.

 

| Task | Command |
| --- | --- |
| Display context information for process exceptions. | display exception context \[ count value ] \[ slot slot-number \[ cpu cpu-number ] ] || Display the core dump file directory. | display exception filepath \[ slot slot-number \[ cpu cpu-number ] ] || Display log information for all user processes. | display process log \[ slot slot-number \[ cpu cpu-number ] ] || Display memory usage for all user processes. | display process memory \[ slot slot-number \[ cpu cpu-number ] ] || Display heap memory usage for a user process. | display process memory heap job job-id \[ verbose ] \[ slot slot-number \[ cpu cpu-number ] ] || Display information about used memory blocks for a user process. | display process memory fragment free job job-id \[ slot slot-number \[ cpu cpu-number ] ] || Display heap memory usage for a user process. | display process memory fragment used used-block job job-id \[ slot slot-number \[ cpu cpu-number ] ] || Display memory content starting from a specified memory block for a user process. | display process memory heap job job-id address starting-address length memory-length \[ slot slot-number \[ cpu cpu-number ] ] || Display the addresses of memory blocks with a specified size used by a user process. | display process memory heap job job-id size memory-size \[ offset offset-size ] \[ slot slot-number \[ cpu cpu-number ] ] || Clear context information for process exceptions. | reset exception context \[ slot slot-number \[ cpu cpu-number ] ] |










‌

## Monitoring and maintaining kernel threads

### Configuring kernel thread deadloop detection

#### About this task

Kernel threads share resources. If a kernel
thread monopolizes the CPU, other threads cannot run, resulting in a deadloop.

This feature enables the device to detect
deadloops. If a thread occupies the CPU for a specific interval, the device determines
that a deadloop has occurred and generates a deadloop message.

#### Restrictions and guidelines

‌

| CAUTION | CAUTION:  Change kernel thread deadloop detection settings only under the guidance of H3C Support. Inappropriate configuration can cause system breakdown. As a best practice, leave the default unchanged. |
| --- | --- |

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable kernel thread deadloop detection.

monitor kernel deadloop enable \[ slot slot-number \[ cpu cpu-number \[ core core-number\&\<1-64\> ] ] ]

By default, kernel thread deadloop
detection is enabled. 

**3\.**(Optional.) Set the threshold for
identifying a kernel thread deadloop.

monitor kernel deadloop time time \[ slot slot-number \[ cpu cpu-number ] ]

By default, the threshold for identifying
a kernel thread deadloop is 20 seconds. 

**4\.**(Optional.) Exclude a kernel thread
from kernel thread deadloop detection.

monitor kernel deadloop exclude-thread tid \[ slot slot-number \[ cpu cpu-number ] ]

When enabled, kernel thread deadloop
detection monitors all kernel threads by default.

**5\.**(Optional.) Specify the action to be
taken in response to a kernel thread deadloop.

monitor kernel deadloop action { reboot \| record-only } \[ slot slot-number \[ cpu cpu-number ] ]

The default action is reboot. 

### Configuring kernel thread starvation detection

#### About this task

Starvation occurs when a thread is unable
to access shared resources.

Kernel thread starvation detection enables the
system to detect and report thread starvation. If a thread is not executed
within a specific interval, the system determines that a starvation has occurred
and generates a starvation message.

Thread starvation does not impact system
operation. A starved thread can automatically run when certain conditions are
met.

#### Restrictions and guidelines

| CAUTION | CAUTION:  Configure kernel thread starvation detection only under the guidance of H3C Support. Inappropriate configuration can cause system breakdown. As a best practice, leave the default unchanged. |
| --- | --- |

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable kernel thread starvation
detection.

monitor kernel starvation enable \[ slot slot-number \[ cpu cpu-number ] ]

By default, kernel thread starvation
detection is disabled.

**3\.**(Optional.) Set the threshold for
identifying a kernel thread starvation.

monitor kernel starvation time time \[ slot slot-number \[ cpu cpu-number ] ]

By default, the threshold for identifying
a kernel thread starvation is 120 seconds. 

**4\.**(Optional.) Exclude a kernel thread
from kernel thread starvation detection.

monitor kernel starvation exclude-thread tid \[ slot slot-number \[ cpu cpu-number ] ]

When enabled, kernel thread starvation
detection monitors all kernel threads by default.

### Display and maintenance commands for kernel threads

Execute display
commands in any view and reset commands in user view.

 

| Task | Command |
| --- | --- |
| Display kernel thread deadloop detection configuration. | display kernel deadloop configuration \[ slot slot-number \[ cpu cpu-number ] ] || Display kernel thread deadloop information. | display kernel deadloop show-number \[ offset ] \[ verbose ] \[ slot slot-number \[ cpu cpu-number ] ] || Display kernel thread exception information. | display kernel exception show-number \[ offset ] \[ verbose ] \[ slot slot-number \[ cpu cpu-number ] ] || Display kernel thread reboot information. | display kernel reboot show-number \[ offset ] \[ verbose ] \[ slot slot-number \[ cpu cpu-number ] ] || Display kernel thread starvation detection configuration. | display kernel starvation configuration \[ slot slot-number \[ cpu cpu-number ] ] || Display kernel thread starvation information. | display kernel starvation show-number \[ offset ] \[ verbose ] \[ slot slot-number \[ cpu cpu-number ] ] || Clear kernel thread deadloop information. | reset kernel deadloop \[ slot slot-number \[ cpu cpu-number ] ] || Clear kernel thread exception information. | reset kernel exception \[ slot slot-number \[ cpu cpu-number ] ] || Clear kernel thread reboot information. | reset kernel reboot \[ slot slot-number \[ cpu cpu-number ] ] || Clear kernel thread starvation information. | reset kernel starvation \[ slot slot-number \[ cpu cpu-number ] ] |










‌

