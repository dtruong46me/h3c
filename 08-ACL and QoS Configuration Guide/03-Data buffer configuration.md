
# Configuring data buffers

## About data buffers

### Data buffer types

Data buffers temporarily store packets to
avoid packet loss.

The following data buffers are available:

·     Ingress buffer—Stores incoming packets when the CPU is busy.

·     Egress buffer—Stores outgoing packets when network congestion occurs.

[Figure 1](#_Ref63863959) shows
the structure of ingress and egress buffers.

Figure 1 Data buffer structure

![](https://resource.h3c.com/en/202407/12/20240712_11705224_x_Img_x_png_0_2216058_294551_0.png)

‌

### Cell resources

A buffer uses cell resources to store
packets based on packet sizes. A cell resource is 256 bytes. The buffer
allocates one cell resource to a 128-byte packet and two cell resources to a
300-byte packet. 

### Fixed area and shared area

The cell resources have a fixed area and a
shared area.

·     Fixed area—Partitioned into queues, each
of which is equally divided by all the interfaces on the switch, as shown in[Figure 1](#_Ref63863959). When congestion occurs or the CPU is busy, the following rules
apply:

**a.**An interface first uses the relevant queues of
the fixed area to store packets.

**b.**When a queue is full, the interface uses the
corresponding queue of the shared area.

**c.**When the queue in the shared area is also
full, the interface discards subsequent packets.

The system allocates the fixed area among
queues as specified by the user. Even if a queue is not
full, other queues cannot preempt its space. Similarly, the share of a
queue for an interface cannot be preempted by other interfaces even if it is
not full.

·     Shared area—Partitioned into queues, each of which is not equally divided by
the interfaces, as shown in [Figure 1](#_Ref63863959). The
system determines the actual shared-area space for each queue according to user
configuration and the number of packets actually received and sent. If a queue
is not full, other queues can preempt its space.

The system puts packets received or sent on
all interfaces into a queue in the order they arrive. When the queue is full,
subsequent packets are dropped.

Figure 1 Fixed area and shared area

![](https://resource.h3c.com/en/202407/12/20240712_11705225_x_Img_x_png_1_2216058_294551_0.png)

‌

## Restrictions and guidelines: Data buffer configuration

You can configure data buffers either manually
or automatically by enabling the Burst feature. If you have configured data
buffers in one way, delete the configuration before using the other way. Otherwise,
the new configuration does not take effect.

Inappropriate data buffer changes can cause
system problems. Before manually changing data buffer settings, make sure you
understand its impact on your device. As a best practice, use the burst-mode
enable command if the system requires large
buffer spaces.

The buffer total-shared command is not supported on a multichassis IRF fabric. 

## Data buffer tasks at a glance

To configure the data buffer, perform the following
tasks:

·     [Enabling the Burst feature](#_Ref326567982)

·     [Configuring data buffers manually](#_Ref326567989)

## Enabling the Burst feature

#### About this task

The Burst feature enables the device to automatically
allocate cell and packet resources. It is well suited to the following
scenarios:

·     Broadcast or multicast traffic is intensive,
resulting in bursts of traffic.

·     Traffic comes in and goes out in one of the
following ways:

¡     Enters
a device from a high-speed interface and goes out of a low-speed interface.

¡     Enters
from multiple same-rate interfaces at the same time and goes out of an
interface with the same rate. 

The default data buffer settings are
changed after the Burst feature is enabled. You can display the data buffer
settings by using the display buffer command.


#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the Burst feature.

burst-mode enable

By default, the Burst feature is
disabled.

## Configuring data buffers manually

#### About this task

Each type of resources of a buffer, packet
or cell, has a fixed size. After you set the shared-area size for a type of
resources, the rest is automatically assigned to the fixed area.

By default, all queues have an equal share
of the shared area and the fixed area. You can change the maximum shared-area
space and the fixed-area for a queue. The unconfigured queues use the default
settings. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure buffer
assignment rules. Choose the options to configure as needed:

¡     Set
the total shared-area ratio.

buffer egress \[ slot slot-number ] cell total-shared ratio ratio

If this command is not configured, you
can display the default value by using the display buffer command.

¡     Set
the maximum shared-area ratio for a queue.

buffer egress \[ slot slot-number ] cell \[ queue queue-id ] shared ratio ratio

The default setting is 33%.

The actual maximum shared-area space for
each queue is determined based on your configuration and the number of packets
to be received and sent. 

¡     Set
the fixed-area ratio for a queue.

buffer egress \[ slot slot-number ] cell queue queue-id guaranteed ratio ratio

The default setting is 12%.

The sum of fixed-area ratios configured
for all queues cannot exceed the total fixed-area ratio. Otherwise, the
configuration fails.

**3\.**Apply buffer assignment rules.

buffer apply

You cannot directly modify the applied
configuration. To modify the configuration, you must cancel the application,
reconfigure data buffers, and reapply the configuration.

## Display and maintenance commands fordata buffers

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display buffer size settings. | display buffer \[ slot slot-number ] \[ queue \[ queue-id ] ] || Display data buffer usage. | display buffer usage \[ slot slot-number ] |


‌

 

 

