
# Configuring process placement

## About process placement

### Process

A process contains a set of codes and provides
specific functionality. For example, an AAA process provides AAA functions. 

Each process runs in a protected memory
space to prevent problems with one process from impacting the entire system.

### Node

The term "node" in this document
refers to CPUs that run the Comware system. If the IRF fabric has multiple
member devices, each member device has a node.

### Process redundancy

The system backs up each active process
running on one node to all the other nodes. When an active process fails, one
of its standby processes promptly takes over without impacting any other
service. 

Process redundancy provides the following benefits:

·     Improves service availability. 

·     Enables the system to quickly regain reliability
after device status changes in such conditions as removal of an IRF member.

### Process placement

The execution of process placement policies
varies by the location of active processes.

·     An active process running only on the master
device does not support placement optimization. If you configure a process
placement policy for the process, the system displays a configuration failure message.
When such an active process fails, the system automatically restarts the
process. The standby processes are used for active/standby switchover and ISSU.

·     Some active processes can run on either the
master or subordinate device. When such an active process fails, the system
uses a placement policy to select a new active process among standby processes.

### Default process placement policy

The system provides a default process placement
policy that takes effect for all processes. By default, the default process
placement policy defines the following rules:

·     The active process runs on the CPU of the master,
and the standby processes run on the CPU of the subordinate device.

·     A process runs at the location where it ran the last
time and does not move to any other location during startup or operation.

·     The addition of a new node does not impact
current active processes. A new active process selects one node with sufficient
CPU and memory resources. (You can use the display cpu-usage and display memory commands
to view CPU and memory usage information.)

You can modify the default placement policy
in the view you enter by using the placement program default command. You can also configure a placement policy for a specific
process in the view you enter by using the placement program program-name \[ instance instance-name ] command. A placement policy for a
process takes precedence over the default process placement policy.

### Process placement affinities

You can configure the following settings
for a process placement policy to optimize process placement:

·     affinity location-set—Location affinity, the preference for the process to run on a specific
node.

·     affinity location-type—Location type affinity, the preference for the process to run on a
particular type of node. For more information about node types, see "[Configuring a location type affinity](#_Ref296948216)."

·     affinity program—Process affinity, the preference for the process to run on the same
node as a particular process.

·     affinity self—Self affinity, the preference for one instance of the process to
run on the same node as any other instance of the process. 

Affinities include positive affinities (attract) and negative affinities (repulse),
all represented by integers in the range of 1 to 100000\. 

·     The higher the attract value, the stronger the
preference.

·     The higher the repulse value, the weaker the
preference.

### Process placement optimization

After you apply new placement policies, the
system makes placement decisions based on the new policies, node resources, and
topology status. If the new location for an active process is different from the
current node, the system changes the state of the process to standby, and uses
the standby process on the preferred location as the new active process.

## Restrictions and guidelines: process placement configuration

·     Configuring process placement on an IRF fabric with
only one member device does not change the location of processes. All processes
run on the CPU of the IRF member device.

·     Configuring process placement on an IRF fabric
with multiple member devices places specific active processes to specific CPUs.
In case of multiple CPUs, the system performs process redundancy. The number of
standby processes and their CPU locations vary by function module. The system by
default automatically determines the location for each active process, and
process placement optimization is not required. If optimization is required, contact
H3C Support to avoid service interruption.

·     For an instance of a process, the settings in
placement policy view of the instance, in placement policy view of the process,
and in default placement policy view are in descending order of priority. For a
process, the settings in placement policy view of the process take precedence
over the settings in default placement policy view.

·     To view the current location of an active
process and its predicted new location after optimization, use the display placement reoptimize
command.

## Process placement tasks at a glance

To configure process placement, perform the
following tasks:

**1\.**[Configuring process placement policy](#_Ref296948213)

Choose the following tasks as needed:

¡     [Configuring a location affinity](#_Ref296948215)

¡     [Configuring a location type affinity](#_Ref296948216)

¡     [Configuring a process affinity](#_Ref296948217)

¡     [Configuring a self affinity](#_Ref296948218)

**2\.**[Optimizing process placement](#_Ref296948224)

## Configuring process placement policy

### Configuring a location affinity

**1\.**Enter system view.

system-view 

**2\.**Enter placement process view.

¡     Enter
default placement process view.

placement program default 

¡     Enter
specified placement process view.

placement program { program-name \[ instance instance-name ]

Settings in default placement process
view take effect for all processes. Settings in placement process view take
effect only for the specified process.

**3\.**Set the location affinity.

affinity location-set { slot slot-number
}\&\<1-5\> { attract strength \| default \| none \| repulse strength }

By default, no location affinity is set.

### Configuring a location type affinity

#### About this task

The following location types are available:

·     current—Current location of the active process, which can be displayed with
the display
placement program command.

·     paired—Locations of standby processes.

·     primary—Master device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter placement process view.

¡     Enter
default placement process view.

placement program default

¡     Enter
specified placement process view.

placement program { program-name \[ instance instance-name ]

Settings in default placement process
view take effect for all processes. Settings in placement process view take
effect only for the specified process.

**3\.**Set the location type affinity.

affinity location-type { current \| paired \| primary } { attract strength \| repulse strength \| default \| none }

By default, no location type affinity is
set.

### Configuring a process affinity

**1\.**Enter system view.

system-view 

**2\.**Enter placement process view.

¡     Enter
default placement process view.

placement program default 

¡     Enter
specified placement process view.

placement program { program-name \[ instance instance-name ]

Settings in default placement process
view take effect for all processes. Settings in placement process view take
effect only for the specified process.

**3\.**Configure the affinity for the process to
run on the same location as another process.

affinity program program-name { attract strength \| default \| none \| repulse strength }

By default, no process affinity is set. 

### Configuring a self affinity

#### About this task

A self affinity determines the preference
for one instance of a process to run on the same node as any other instance of
the process. The self affinity setting does not take effect for a process that
has only one instance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter placement process view.

¡     Enter
default placement process view.

placement program default

¡     Enter
specified placement process view.

placement program { program-name \[ instance instance-name ]

Settings in default placement process
view take effect for all processes. Settings in placement process view take
effect only for the specified process.

**3\.**Configure a self affinity.

affinity self { attract strength \| repulse strength \| default \| none }

By default, no self affinity is set.

## Optimizing process placement

#### Restrictions and guidelines

| CAUTION | CAUTION:  To avoid neighbor flapping of related protocols, make sure HA features such as NSR or GR are configured for the processes and are stable before optimizing process placement. |
| --- | --- |

 

To keep the system stable, do not perform
any tasks that require process restart when you optimize process placement.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Optimize process placement.

placement reoptimize 

## Display and maintenance commands for process placement

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display service group information. | display ha service-group { program-name \[ instance instance-name ] \| all } || Display the running processes on a specific location. | display placement location { all \| slot slot-number } || Display process placement policy information. | display placement policy program { program-name \| all \| default } || Display the location of a process. | display placement program { program-name \| all } || Display the predicted location of a process after process placement optimization. | display placement reoptimize program { program-name \[ instance instance-name ] \| all } |





‌

 

