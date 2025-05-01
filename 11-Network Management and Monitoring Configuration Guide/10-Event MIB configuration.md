
# Configuring the Event MIB

## About the Event MIB

The Event Management Information Base
(Event MIB) is an SNMPv3-based network management protocol and is an
enhancement to remote network monitoring (RMON). The Event MIB uses Boolean
tests, existence tests, and threshold tests to monitor MIB objects on a local
or remote system. It triggers the predefined notification or set action when a
monitored object meets the trigger condition.

### Trigger

The Event MIB uses triggers to manage and associate
the three elements of the Event MIB: monitored object, trigger condition, and
action.

### Monitored objects

The Event MIB can monitor the following MIB
objects: 

·     Table node.

·     Conceptual row node.

·     Table column node.

·     Simple leaf node.

·     Parent node of a leaf node.

To monitor a single MIB object, specify it
by its OID or name. To monitor a set of MIB objects, specify the common OID or
name of the group and enable wildcard matching. For example, specify ifDescr.2 to
monitor the description for the interface with index 2\. Specify ifDescr and enable
wildcard matching to monitor the descriptions for all interfaces. 

### Trigger test

A trigger supports Boolean, existence, and
threshold tests.

#### Boolean test

A Boolean test compares the value of the
monitored object with the reference value and takes actions according to the
comparison result. The comparison types include unequal,
equal, less, lessorequal, greater, and greaterorequal. For example, if the comparison type is equal, an event is triggered when the value of the
monitored object equals the reference value. The event will not be triggered
again until the value becomes unequal and comes back to equal.

#### Existence test

An existence test monitors and manages the
absence, presence, and change of a MIB object, for example, interface status.
When a monitored object is specified, the system reads the value of the
monitored object regularly.

·     If the test type is Absent,
the system triggers an alarm event and takes the specified action when the state
of the monitored object changes to absent.

·     If the test type is Present,
the system triggers an alarm event and takes the specified action when the state
of the monitored object changes to present.

·     If the test type is Changed,
the system triggers an alarm event and takes the specified action when the
value of the monitored object changes.

#### Threshold test

A threshold test regularly compares the
value of the monitored object with the threshold values.

·     A rising alarm event is triggered if the value
of the monitored object is greater than or equal to the rising threshold.

·     A falling alarm event is triggered if the value
of the monitored object is smaller than or equal to the falling threshold. 

·     A rising alarm event is triggered if the
difference between the current sampled value and the previous sampled value is
greater than or equal to the delta rising threshold.

·     A falling alarm event is triggered if the
difference between the current sampled value and the previous sampled value is
smaller than or equal to the delta falling threshold.

·     A falling alarm event is triggered if the values
of the monitored object, the rising threshold, and the falling threshold are
the same.

·     A falling alarm event is triggered if the delta rising
threshold, the delta falling threshold, and the difference between the current
sampled value and the previous sampled value is the same.

The alarm management module defines the set
or notification action to take on alarm events. 

If the value of the monitored object
crosses a threshold multiple times in succession, the managed device triggers
an alarm event only for the first crossing. For example, if the value of a
sampled object crosses the rising threshold multiple times before it crosses
the falling threshold, only the first crossing triggers a rising alarm event,
as shown in [Figure 1](#_Ref350258809).

Figure 1 Rising and falling alarm events

![](https://resource.h3c.com/en/202407/12/20240712_11705749_x_Img_x_png_0_2216115_294551_0.png)

‌

### Event actions

The Event MIB triggers one or both of the
following actions when the trigger condition is met:

·     Set action—Uses SNMP to set the value of the monitored object.

·     Notification action—Uses SNMP to send a notification to the NMS. If an object list is specified
for the notification action, the notification will carry the specified objects
in the object list.

### Object list

An object list is a set of MIB objects. You
can specify an object list in trigger view, trigger-test view (including
trigger-Boolean view, trigger existence view, and trigger threshold view), and
action-notification view. If a notification action is triggered, the device
sends a notification carrying the object list to the NMS.

If you specify an object list respectively
in any two of the views or all the three views, the object lists are added to
the triggered notifications in this sequence: trigger view, trigger-test view,
and action-notification view.

### Object owner

Trigger, event, and object list use an
owner and name for unique identification. The owner must be an SNMPv3 user that
has been created on the device. If you specify a notification action for a
trigger, you must establish an SNMPv3 connection between the device and NMS by
using the SNMPv3 username. For more information about SNMPv3 user, see
"SNMP configuration". 

## Restrictions and guidelines: Event MIB configuration

The Event MIB and RMON are independent of
each other. You can configure one or both of the features for network
management.

You must specify the same owner for a
trigger, object lists of the trigger, and events of the trigger.

## Event MIB tasks at a glance

To configure the Event MIB, perform the
following tasks:

**1\.**[Configuring the Event MIB global
sampling parameters](#_Ref463806360)

**2\.**(Optional.) [Configuring Event MIB object lists](#_Ref421180004)

Perform this task so that the device
sends a notification that carries the specified object list to the NMS when a
notification action is triggered.

**3\.**[Configuring an event](#_Ref408997228)

The device supports set and notification
actions. Choose one or two of the following actions: 

¡     [Creating an event](#_Ref408997245)

¡     [Configuring a set action for an event](#_Ref408997248)

¡     [Configuring a notification action for an
event](#_Ref462910980)

¡     [Enabling the event](#_Ref462910981)

**4\.**[Configuring a trigger](#_Ref408997257)

A trigger supports Boolean, existence, and
threshold tests. Choose one or more of the following tests:

¡     [Creating a trigger and configuring its basic
parameters](#_Ref462911087)

¡     [Configuring a Boolean trigger test](#_Ref408997547)

¡     [Configuring an existence trigger test](#_Ref408997549)

¡     [Configuring a threshold trigger test](#_Ref408997551)

¡     [Enabling trigger sampling](#_Ref462911052)

**5\.**(Optional.) [Enabling SNMP notifications for the
Event MIB module](#_Ref463807808)

## Prerequisites for configuring the Event MIB

Before you configure the Event MIB, perform
the following tasks:

·     Create an SNMPv3 user. Assign the user the
rights to read and set the values of the specified MIB objects and object lists.

·     Make sure the SNMP agent and NMS are configured
correctly and the SNMP agent can send notifications to the NMS correctly.

## Configuring the Event MIB global sampling parameters

#### Restrictions and guidelines

This tasks takes effect only on monitored
instances to be created. 

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Set the minimum sampling interval. 

snmp mib event sample minimum min-number 

By default, the minimum sampling interval
is 1 second.

The sampling interval of a trigger must
be greater than the minimum sampling interval.

**3\.**Configure the maximum number of object
instances that can be concurrently sampled.

snmp mib event sample instance maximum max-number 

By default, the value is 0\. The maximum
number of object instances that can be concurrently sampled is limited by the available
resources.

## ConfiguringEvent MIB object lists

#### About this task

Perform this task so that the device sends
a notification that carries the specified objects to the NMS when a
notification action is triggered.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Configure an Event MIB object list.

snmp mib event object list owner group-owner name group-name object-index oid object-identifier
\[ wildcard ]

The object can be a table node, conceptual
row node, table column node, simple leaf node, or parent node of a leaf node.

## Configuring an event

### Creating an event

**1\.**Enter system view.

system-view 

**2\.**Create an event and enter its view.

snmp mib event owner event-owner name event-name 

**3\.**(Optional.) Configure a description for the
event.

description text 

By default, an event does not have a
description.

### Configuring a set action for an event

**1\.**Enter system view.

system-view 

**2\.**Enter event view.

snmp mib event owner event-owner name event-name 

**3\.**Enable the set action and enter set action
view.

action set 

By default, no action is specified for an
event.

**4\.**Specify an object by its OID for the set
action.

oid object-identifier 

By default, no object is specified for a
set action.

The object can be a table node, conceptual
row node, table column node, simple leaf node, or parent node of a leaf node.

**5\.**Enable OID wildcarding.

wildcard oid 

By default, OID wildcarding is disabled.

**6\.**Set the value for the object.

value integer-value 

The default value for the object is 0\.

**7\.**(Optional.) Specify a context for the
object.

context context-name 

By default, no context is specified for
an object.

**8\.**(Optional.) Enable context wildcarding.

wildcard context 

By default, context wildcarding is
disabled.

A wildcard context contains the specified
context and the wildcarded part.

### Configuring a notification action for an event

**1\.**Enter system view.

system-view 

**2\.**Enter event view

snmp mib event owner event-owner name event-name 

**3\.**Enable the notification action and enter
notification action view.

action notification 

By default, no action is specified for an
event.

**4\.**Specify an object to execute the
notification action by its OID.

oid object-identifier 

By default, no object is specified for executing
the notification action.

The object must be a notification object.

**5\.**Specify an object list to be added to the
notification triggered by the event.

object list owner group-owner name group-name 

By default, no object list is specified
for the notification action.

If you do not specify an object list for
the notification action or the specified object list does not contain
variables, no variables will be carried in the notification.

### Enabling the event

#### Restrictions and guidelines

The Boolean, existence, and threshold events
can be triggered only after you perform this task.

To change an enabled event, first disable
the event.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter event view.

snmp mib event owner event-owner name event-name

**3\.**Enable the event.

event enable 

By default, an event is disabled.

## Configuring a trigger

### Creating a trigger and configuring its basic parameters

**1\.**Enter system view.

system-view

**2\.**Create a trigger and enter its view.

snmp mib event trigger owner trigger-owner name trigger-name

The trigger owner must be an existing
SNMPv3 user.

**3\.**(Optional.) Configure a description for the
trigger.

description text

By default, a trigger does not have a description.

**4\.**Set a sampling interval for the trigger.

frequency interval

By default, the sampling interval is 600
seconds.

Make sure the sampling interval is
greater than or equal to the Event MIB minimum sampling interval.

**5\.**Specify a sampling method.

sample { absolute \| delta }

The default sampling method is absolute.

**6\.**Specify an object to be sampled by its OID.

oid object-identifier

By default, the OID is 0.0. No object is
specified for a trigger.

If you execute this command multiple
times, the most recent configuration takes effect.

**7\.**(Optional.) Enable OID wildcarding.

wildcard oid

By default, OID wildcarding is disabled.

**8\.**(Optional.) Configure a context for the
monitored object.

context context-name

By default, no context is configured for
a monitored object.

**9\.**(Optional.) Enable context wildcarding.

wildcard context

By default, context wildcarding is
disabled.

**10\.**(Optional.) Specify the object list to be
added to the triggered notification.

object list owner group-owner name group-name

By default, no object list is specified
for a trigger.

### Configuring a Boolean trigger test

**1\.**Enter system view.

system-view 

**2\.**Enter trigger view.

snmp mib event trigger owner trigger-owner name trigger-name 

**3\.**Specify a Boolean test for the trigger and
enter trigger-Boolean view.

test boolean 

By default, no test is configured for a
trigger.

**4\.**Specify a Boolean test comparison type.

comparison { equal \| greater \| greaterorequal \| less \| lessorequal \| unequal }

The default Boolean test comparison type
is unequal.

**5\.**Set a reference value for the Boolean
trigger test.

value integer-value 

The default reference value for a Boolean
trigger test is 0\.

**6\.**Specify an event for the Boolean trigger
test.

event owner event-owner name event-name 

By default, no event is specified for a
Boolean trigger test.

**7\.**(Optional.) Specify the object list to be
added to the notification triggered by the test.

object list owner group-owner name group-name 

By default, no object list is specified
for a Boolean trigger test.

**8\.**Enable the event to be triggered when the
trigger condition is met at the first sampling.

startup enable 

By default, the event is triggered when
the trigger condition is met at the first sampling.

Before the first sampling, you must
enable this command to allow the event to be triggered.

### Configuring an existence trigger test

**1\.**Enter system view.

system-view 

**2\.**Enter trigger view.

snmp mib event trigger owner trigger-owner name trigger-name

**3\.**Specify an existence test for the trigger
and enter trigger-existence view.

test existence 

By default ,no test is configured for a
trigger.

**4\.**Specify an event for the existence trigger
test.

event owner event-owner name event-name 

By default, no event is specified for an
existence trigger test.

**5\.**(Optional.) Specify the object list to be
added to the notification triggered by the test.

object list owner group-owner name group-name 

By default, no object list is specified
for an existence trigger test.

**6\.**Specify an existence trigger test type.

type { absent \| changed \| present }

The default existence trigger test types
are present and absent.

**7\.**Specify an existence trigger test type for
the first sampling.

startup { absent \| present }

By default, both the present and absent existence
trigger test types are allowed for the first sampling.

### Configuring a threshold trigger test

**1\.**Enter system view.

system-view

**2\.**Enter trigger view.

snmp mib event trigger owner trigger-owner name trigger-name

**3\.**Specify a threshold test for the trigger and
enter trigger-threshold view.

test boolean

By default ,no test is configured for a
trigger.

**4\.**Specify the object list to be added to the
notification triggered by the test.

object list owner group-owner name group-name

By default, no object list is specified
for a threshold trigger test.

**5\.**(Optional.) Specify the type of the
threshold trigger test for the first sampling.

startup { falling \| rising \| rising-or-falling }

The default threshold trigger test type
for the first sampling is rising-or-falling.

**6\.**Specify the delta falling threshold and the
falling alarm event triggered when the delta value (difference between the
current sampled value and the previous sampled value) is smaller than or equal
to the delta falling threshold.

delta falling { event owner event-owner name event-name \| value integer-value }

By default, the delta falling threshold
is 0, and no falling alarm event is specified.

**7\.**Specify the delta rising threshold and the
rising alarm event triggered when the delta value is greater than or equal to
the delta rising threshold.

delta rising { event owner event-owner name event-name \| value integer-value }

By default, the delta rising threshold is
0, and no rising alarm event is specified.

**8\.**Specify the falling threshold and the
falling alarm event triggered when the sampled value is smaller than or equal
to the threshold.

falling { event owner event-owner name event-name \| value integer-value }

By default, the falling threshold is 0,
and no falling alarm event is specified.

**9\.**Specify the rising threshold and the ring
alarm event triggered when the sampled value is greater than or equal to the
threshold.

rising { event owner event-owner name event-name \| value integer-value }

By default, the rising threshold is 0,
and no rising alarm event is specified.

### Enabling trigger sampling

#### Restrictions and guidelines

Enable trigger sampling after you complete trigger
parameters configuration. You cannot modify trigger parameters after trigger
sampling is enabled. To modify trigger parameters, first disable trigger
sampling.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter trigger view.

snmp mib event trigger owner trigger-owner name trigger-name

**3\.**Enable trigger sampling.

trigger enable

By default, trigger sampling is disabled.

## Enabling SNMP notifications for the Event MIB module

#### About this task

To report critical Event MIB events to an
NMS, enable SNMP notifications for the Event MIB module. For Event MIB event
notifications to be sent correctly, you must also configure SNMP on the device.
For more information about SNMP configuration, see the network management and
monitoring configuration guide for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable snmp notifications for the Event MIB
module.

snmp-agent trap enable event-mib

By default, SNMP notifications are
enabled for the Event MIB module.

## Display and maintenance commands for Event MIB

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display Event MIB configuration and statistics. | display snmp mib event || Display event information. | display snmp mib event event \[ owner event-owner name event-name ] || Display object list information. | display snmp mib event object list \[ owner group-owner name group-name ] || Display global Event MIB configuration and statistics. | display snmp mib event summary || Display trigger information. | display snmp mib event trigger \[ owner trigger-owner name trigger-name ] |





‌

## Event MIB configuration examples

### Example: Configuring an existence trigger test

#### Network configuration

As shown in [Figure 2](#_Ref324493003), the
device acts as the agent. Use the Event MIB to monitor the device. When
interface hot-swap or virtual interface creation or deletion occurs on the
device, the agent sends an mteTriggerFired notification to the NMS.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705750_x_Img_x_png_1_2216115_294551_0.png)

‌

#### Procedure

 

**1\.**Enable and configure the SNMP agent on the
device:

\# Create SNMPv3 group g3 and add
SNMPv3 user owner1 to g3.

\<Sysname\> system-view

\[Sysname] snmp-agent usm-user
v3 owner1 g3

\[Sysname] snmp-agent group v3
g3 read-view a write-view a notify-view a

\[Sysname] snmp-agent mib-view
included a iso

\# Configure context contextnameA for
the agent.

\[Sysname] snmp-agent context
contextnameA

\# Enable SNMP notifications for the Event
MIB module. Specify the NMS at 192.168.1.26 as the
target host for the notifications.

\[Sysname] snmp-agent trap
enable event-mib

\[Sysname] snmp-agent
target-host trap address udp-domain 192.168.1.26 params securityname owner1 v3

**2\.**Configure the Event MIB global sampling
parameters.

\# Set the Event MIB minimum
sampling interval to 50 seconds.

\[Sysname] snmp mib event
sample minimum 50

\# Set the maximum number to 100 for
object instances that can be concurrently sampled.

\[Sysname] snmp mib event
sample instance maximum 100

**3\.**Create and configure a trigger:

\# Create a trigger and enter its view. Specify
its owner as owner1 and its name as triggerA. 

\[Sysname] snmp mib event
trigger owner owner1 name triggerA

\# Set the sampling interval to 60 seconds.
Make sure the sampling interval is greater than or equal to the Event MIB
minimum sampling interval.

\[Sysname-trigger-owner1-triggerA]
frequency 60

\# Specify object OID 1.3.6.1.2.1.2.2.1.1 as the monitored object. Enable OID wildcarding.

\[Sysname-trigger-owner1-triggerA]
oid 1.3.6.1.2.1.2.2.1.1

\[Sysname-trigger-owner1-triggerA]
wildcard oid

\# Configure context contextnameA for
the monitored object and enable context wildcarding.

\[Sysname-trigger-owner1-triggerA]
context contextnameA

\[Sysname-trigger-owner1-triggerA]
wildcard context

\# Specify the existence trigger test for
the trigger.

\[Sysname-trigger-owner1-triggerA]
test existence

\[Sysname-trigger-owner1-triggerA-existence]
quit

\# Enable trigger sampling.

\[Sysname-trigger-owner1-triggerA]
trigger enable

\[Sysname-trigger-owner1-triggerA]
quit

#### Verifying the configuration

\# Display Event MIB brief information.

\[Sysname] display snmp mib event
summary

TriggerFailures               : 0

EventFailures                 : 0

SampleMinimum                 : 50

SampleInstanceMaximum         : 100

SampleInstance                : 20

SampleInstancesHigh           : 20

SampleInstanceLacks           : 0

\# Display information about the trigger
with owner owner1 and name trigger
A.

\[Sysname] display snmp mib event trigger
owner owner1 name triggerA

Trigger entry triggerA owned by owner1:

  TriggerComment              : N/A

  TriggerTest                 : existence

  TriggerSampleType           : absoluteValue

  TriggerValueID              :
1.3.6.1.2.1.2.2.1.1\<ifIndex\>

  TriggerValueIDWildcard      : true

  TriggerTargetTag            : N/A

  TriggerContextName          :
contextnameA

  TriggerContextNameWildcard  : true

  TriggerFrequency(in seconds): 60

  TriggerObjOwner             : N/A

  TriggerObjName              : N/A

  TriggerEnabled              : true

  Existence entry:

   ExiTest                    : present
\| absent

   ExiStartUp                 : present
\| absent

   ExiObjOwner                : N/A

   ExiObjName                 : N/A

   ExiEvtOwner                : N/A

   ExiEvtName                 : N/A

\# Create VLAN-interface 2 on the device.

\[Sysname] vlan 2

\[Sysname-vlan2] quit

\[Sysname] interface vlan 2

The NMS receives an mteTriggerFired
notification from the device.

### Example: Configuring a Boolean trigger test

#### Network configuration

As shown in [Figure 2](#_Ref324493003),
the device acts as the agent. The NMS uses SNMPv3 to monitor and manage the
device. Configure a trigger and configure a Boolean trigger test for the
trigger. When the trigger condition is met, the agent sends an mteTriggerFired
notification to the NMS.

Figure 3 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705751_x_Img_x_png_2_2216115_294551_0.png)

‌

#### Procedure

 

**1\.**Enable and configure the SNMP agent on the
device:

\# Create SNMPv3 group g3 and add SNNPv3 user owner1 to g3.

\<Sysname\> system-view

\[Sysname] snmp-agent usm-user
v3 owner1 g3

\[Sysname] snmp-agent group v3
g3 read-view a write-view a notify-view a

\[Sysname] snmp-agent mib-view
included a iso

\# Enable SNMP notifications for the Event
MIB module. Specify the NMS at 192.168.1.26 as the
target host for the notifications.

\[Sysname] snmp-agent trap
enable event-mib

\[Sysname] snmp-agent
target-host trap address udp-domain 192.168.1.26 params securityname owner1 v3

**2\.**Configure the Event MIB global sampling parameters.

\# Set the Event MIB
minimum sampling interval to 50 seconds.

\[Sysname] snmp mib event
sample minimum 50

\# Set the maximum number to 100 for
object instances that can be concurrently sampled.

\[Sysname] snmp mib event
sample instance maximum 100

**3\.**Configure Event MIB object lists objectA, objectB, and objectC.

\[Sysname] snmp mib event
object list owner owner1 name objectA 1 oid 1.3.6.1.4.1.25506.2.6.1.1.1.1.6.11

\[Sysname] snmp mib event
object list owner owner1 name objectB 1 oid 1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11

\[Sysname] snmp mib event
object list owner owner1 name objectC 1 oid 1.3.6.1.4.1.25506.2.6.1.1.1.1.8.11

**4\.**Configure an event:

\# Create an event and enter its view. Specify
its owner as owner1 and its name as EventA.

\[Sysname] snmp mib event owner
owner1 name EventA

\# Specify the notification action for the
event. Specify object OID 1.3.6.1.4.1.25506.2.6.2.0.5
(hh3cEntityExtMemUsageThresholdNotification) to
execute the notification.

\[Sysname-event-owner1-EventA]
action notification

\[Sysname-event-owner1-EventA-notification]
oid 1.3.6.1.4.1.25506.2.6.2.0.5

\# Specify the object list with owner owner 1 and name
objectC to be added to the notification when the notification action is
triggered

\[Sysname-event-owner1-EventA-notification]
object list owner owner1 name objectC

\[Sysname-event-owner1-EventA-notification]
quit

\# Enable the event.

\[Sysname-event-owner1-EventA]
event enable

\[Sysname-event-owner1-EventA]
quit

**5\.**Configure a trigger:

\# Create a trigger and enter its view. Specify
its owner as owner1 and its name as triggerA. 

\[Sysname] snmp mib event
trigger owner owner1 name triggerA

\# Set the sampling interval to 60 seconds.
Make sure the interval is greater than or equal to the global minimum sampling
interval.

\[Sysname-trigger-owner1-triggerA]
frequency 60

\# Specify object OID 1.3.6.1.4.1.25506.2.6.1.1.1.1.9.11 as the monitored object.

\[Sysname-trigger-owner1-triggerA]
oid 1.3.6.1.4.1.25506.2.6.1.1.1.1.9.11

\# Specify the object list with owner owner1 and name objectA to be added
to the notification when the notification action is triggered.

\[Sysname-trigger-owner1-triggerA]
object list owner owner1 name objectA

\# Configure a Boolean trigger test. Set
its comparison type to greater, reference value to 10, and specify the event
with owner owner1 and name EventA, object list with owner owner1 and name objectB for the
test.

\[Sysname-trigger-owner1-triggerA]
test boolean

\[Sysname-trigger-owner1-triggerA-boolean]
comparison greater

\[Sysname-trigger-owner1-triggerA-boolean]
value 10

\[Sysname-trigger-owner1-triggerA-boolean]
event owner owner1 name EventA

\[Sysname-trigger-owner1-triggerA-boolean]
object list owner owner1 name objectB

\[Sysname-trigger-owner1-triggerA-boolean]
quit

\# Enable trigger sampling.

\[Sysname-trigger-owner1-triggerA]
trigger enable

\[Sysname-trigger-owner1-triggerA]
quit

#### Verifying the configuration

\# Display Event MIB configuration and
statistics.

\[Sysname] display snmp mib event
summary

TriggerFailures               : 0

EventFailures                 : 0

SampleMinimum                 : 50

SampleInstanceMaximum         : 10

SampleInstance                : 1

SampleInstancesHigh           : 1

SampleInstanceLacks           : 0

\# Display information about the Event MIB object
lists.

\[Sysname] display snmp mib event
object list

Object list objectA owned by owner1:

  ObjIndex                    : 1

  ObjID                       :
1.3.6.1.4.1.25506.2.6.1.1.1.1.6.11\<hh3cEntityExt

CpuUsage.11\> 

  ObjIDWildcard               : false

Object list objectB owned by owner1:

  ObjIndex                    : 1

  ObjID                       :
1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11\<hh3cEntityExt

CpuUsageThreshold.11\> 

  ObjIDWildcard               : false

Object list objectC owned by owner1: 

  ObjIndex                    : 1

  ObjID                       :
1.3.6.1.4.1.25506.2.6.1.1.1.1.8.11\<hh3cEntityExt

MemUsage.11\>

  ObjIDWildcard               : false

\# Display information about the event.

\[Sysname]display snmp mib event event
owner owner1 name EventA

Event entry EventA owned by owner1:

EvtComment                  : N/A

EvtAction                   :
notification

EvtEnabled                  : true

Notification entry: 

NotifyOID                  :
1.3.6.1.4.1.25506.2.6.2.0.5\<hh3cEntityExtMemUsag

eThresholdNotification\> 

NotifyObjOwner             : owner1

NotifyObjName              : objectC

\# Display information about the trigger.

\[Sysname] display snmp mib event
trigger owner owner1 name triggerA

Trigger entry triggerA owned by owner1:

  TriggerComment              : N/A

  TriggerTest                 : boolean

  TriggerSampleType           : absoluteValue

  TriggerValueID              :
1.3.6.1.4.1.25506.2.6.1.1.1.1.9.11\<hh3cEntityExt

MemUsageThreshold.11\>

  TriggerValueIDWildcard      : false

  TriggerTargetTag            : N/A

  TriggerContextName          : N/A

  TriggerContextNameWildcard  : false

  TriggerFrequency(in seconds): 60

  TriggerObjOwner             : owner1

  TriggerObjName              :
objectA

  TriggerEnabled              : true

  Boolean entry:

   BoolCmp                    : greater

   BoolValue                  : 10

   BoolStartUp                : true

   BoolObjOwner               :
owner1

   BoolObjName                :
objectB

   BoolEvtOwner               :
owner1

   BoolEvtName                :
EventA

\# When the value of the monitored object 1.3.6.1.4.1.25506.2.6.1.1.1.1.9.11
becomes greater than 10, the NMS receives an mteTriggerFired notification.

### Example: Configuring a threshold trigger test

#### Network configuration

As shown in [Figure 2](#_Ref324493003), the
device acts as the agent. The NMS uses SNMPv3 to monitor and manage the device.
Configure a trigger and configure a threshold trigger test for the trigger. When
the trigger conditions are met, the agent sent an mteTriggerFired notification to
the NMS.

Figure 4 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705752_x_Img_x_png_3_2216115_294551_0.png)

‌

#### Procedure

 

 

**1\.**Enable and configure the SNMP agent on the
device:

\# Create SNMPv3 group g3 and add SNMPv3 user owner1 to g3.

\<Sysname\> system-view

\[Sysname] snmp-agent usm-user
v3 owner1 g3

\[Sysname] snmp-agent group v3
g3 read-view a write-view a notify-view a

\[Sysname] snmp-agent mib-view
included a iso

\# Enable SNMP notifications for the Event
MIB module. Specify the NMS at 192.168.1.26 as the
target host for the notifications.

\[Sysname] snmp-agent trap
enable event-mib

\[Sysname] snmp-agent
target-host trap address udp-domain 192.168.1.26 params securityname owner1 v3

\[Sysname] snmp-agent trap
enable

**2\.**Configure the Event MIB global sampling parameters.

\# Set the Event MIB
minimum sampling interval to 50 seconds.

\[Sysname] snmp mib event
sample minimum 50

\# Set the maximum number to 10 for object
instances that can be concurrently sampled.

\[Sysname] snmp mib event
sample instance maximum 10

**3\.**Create and configure a trigger:

\# Create a trigger and enter its view. Specify
its owner as owner1 and its name as triggerA. 

\[Sysname] snmp mib event
trigger owner owner1 name triggerA

\# Set the sampling interval to 60 seconds.
Make sure the interval is greater than or equal to the Event MIB minimum
sampling interval.

\[Sysname-trigger-owner1-triggerA]
frequency 60

\# Specify object OID 1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11 as the monitored object.

\[Sysname-trigger-owner1-triggerA]
oid 1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11

\# Configure a threshold trigger test.
Specify the rising threshold to 80 and the falling threshold to 10 for the
test. 

\[Sysname-trigger-owner1-triggerA]
test threshold

\[Sysname-trigger-owner1-triggerA-threshold]
rising value 80

\[Sysname-trigger-owner1-triggerA-threshold]
falling value 10

\[Sysname-trigger-owner1-triggerA-threshold]
quit

\# Enable trigger sampling.

\[Sysname-trigger-owner1-triggerA]
trigger enable

\[Sysname-trigger-owner1-triggerA]
quit

#### Verifying the configuration

\# Display Event MIB configuration and
statistics.

\[Sysname] display snmp mib event
summary

TriggerFailures               : 0

EventFailures                 : 0

SampleMinimum                 : 50

SampleInstanceMaximum         : 10

SampleInstance                : 1

SampleInstancesHigh           : 1

SampleInstanceLacks           : 0

\# Display information about the trigger.

\[Sysname] display snmp mib event
trigger owner owner1 name triggerA

Trigger entry triggerA owned by owner1:

  TriggerComment              : N/A

  TriggerTest                 : threshold

  TriggerSampleType           : absoluteValue

  TriggerValueID              : 1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11\<hh3cEntityExt

CpuUsageThreshold.11\>

  TriggerValueIDWildcard      : false

  TriggerTargetTag            : N/A

  TriggerContextName          : N/A

  TriggercontextNameWildcard  : false

  TriggerFrequency(in seconds): 60

  TriggerObjOwner             : N/A

  TriggerObjName              : N/A

  TriggerEnabled              : true

Threshold entry:

   ThresStartUp               :
risingOrFalling

   ThresRising                : 80

   ThresFalling               : 10

   ThresDeltaRising           : 0

   ThresDeltaFalling          : 0

   ThresObjOwner              : N/A

   ThresObjName               : N/A

   ThresRisEvtOwner           : N/A

   ThresRisEvtName            : N/A

   ThresFalEvtOwner           : N/A

   ThresFalEvtName            : N/A

   ThresDeltaRisEvtOwner      : N/A

   ThresDeltaRisEvtName       : N/A

   ThresDeltaFalEvtOwner      : N/A

   ThresDeltaFalEvtName       : N/A

\# When the rising threshold of the
monitored object 1.3.6.1.4.1.25506.2.6.1.1.1.1.7.11 is greater than 80, the NMS
receives an mteTriggerFired notification.

